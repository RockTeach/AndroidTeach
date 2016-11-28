# 为网络请求添加缓存 #
<p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在以往的开发中，我们想要为页面添加缓存，通常会使用数据库或者文件的形式进行缓存，缓存做起来相对复杂。这里我们将使用OkHttp的制作一下页面缓存，体验一下什么叫做feel倍爽
。OkHttp简单的常规用法，这里我们就不进行赘述了，今天我们主要实现OkHttp网络请求的缓存</p>

- 通常我们在使用OkHttp做请求的时候是这个样子的
<pre>

    OkHttpClient okHttpClient = new OkHttpClient();

    Request request = new Request.Builder()
                                    .url("http://xxx.xxx/xxx")
                                    .build();

    Call call = okHttpClient.newCall(request);

    call.enqueue(new Callback() {
        @Override
        public void onFailure(Call call, IOException e) {

        }

        @Override
        public void onResponse(Call call, Response response) throws IOException {

        }
    });
</pre>
- 在项目中我们知道这个OkHttpClient不需要重复创建，全局使用一个就Ok的
- 如果我们有一些通用的配置就可以在这个OkHttp上进行配置，但是我们发现OkHttpClient这个对象好像没有提供太多的方法供我们调用，经过翻读源码，我们发现在new OkHttpClient对象的时候，它是使用Builder进行构建的
- 实际上当你学过用过的知识点多了之后，你会发现很多配置型的数据都会使用这种建造者模式进行参数配置
- 实例化可以变成这个样子
<code>OkHttpClient httpClient = new OkHttpClient.Builder().build();</code>
- 通过这个建造者我们发现这OkHttpClient还是有很多可配置的属性的，其中就有一个叫做 cache 的方法，通过这个方法呢我们就可以实现请求数据的缓存，但是这个方法需要服务器的配合（也就是服务器本身要支持缓存），需要服务器返回的Response中包含
  <code>Header:Cache-Control,max-age=xxx</code>,如果服务器支持缓存，那么我们的OkHttp在初始化的时候只需为它指定一个缓存路径就OK了
<pre>
Cache cache = new Cache(getCacheDir(),50 * 1024 * 1024);

        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                // 设置缓存目录
                .cache(cache)

                .build();
</pre>
- 但是在我们开发的过程中，很多时候服务器并没有实现缓存机制，而我们又想使用这个缓存怎么办呢？经研究发现我们可以使用建造OkHttpClient时
  的一个方法，为OkHttpClient添加网络拦截器来实现
<pre>
Cache cache = new Cache(getCacheDir(),50 * 1024 * 1024);

        Interceptor cacheInterCepter = new Interceptor() {
            @Override
            public Response intercept(Chain chain) throws IOException {

                Request request = chain.request();
                // 在请求前判断网络,没有网络将请求设置为强制从缓存获取
                if (!NetWorkUtil.isNetConnected(TeachApp.this)) {
                    request = request.newBuilder()
                                    .cacheControl(CacheControl.FORCE_CACHE)
                                    .build();
                }
                // 获取请求的结果
                Response response = chain.proceed(request);
                // 根据网络状况，对结果进行不同转换
                if (NetWorkUtil.isNetConnected(TeachApp.this)) {
                    String cacheControl = request.cacheControl().toString();
                    response = response.newBuilder()
                                        .removeHeader("Pragma")
                                        .addHeader("Cache-Control",cacheControl)
                                        .build();
                }else{
                    response = response.newBuilder()
                                        .removeHeader("Pragma")
                                        .addHeader("Cache-Control","public,only-if-cached,max-stale" + 60 * 60 * 24 * 2)
                                        .build();
                }
                return response;
            }
        };



        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                // 设置缓存目录
                .cache(cache)
                // 添加网络拦截器
                .addNetworkInterceptor(cacheInterCepter)
                .build();

</pre>
- 在网络请求执行的时候对请求以及响应做包装，使我们的请求和响应支持缓存，那么我们在开发中到底应该怎样调用呢？其实也很简单，在请求的时候添加Header就可以实现了
- 原生OkHttp的写法
<pre>
Request request = new Request.Builder()
                              .url("http://xxx.xxx/xxx")
                              // 10 秒内的重复请求走缓存
                              .addHeader("Cache-Control","max-age=" + 10)
                              .build();

        Call call = okHttpClient.newCall(request);

        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {

            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {

            }
        });

</pre>
- OkHttp-Utils用法
<pre>
OkHttpUtils.get()
            .url("http://xxx.xxx")
            // 五秒内的重复请求走缓存
            .addHeader("Cache-Control","max-age=" + 5)
            .build()
            .execute(new StringCallback() {
                @Override
                public void onError(Call call, Exception e, int id) {

                }

                @Override
                public void onResponse(String response, int id) {

                }
              });
</pre>
- Retrofit的用法
<pre>
@GET("/")
Call<String> getString(@Header("Cache-Control") String cacheControl);
</pre>
<pre>
Retrofit retrofit = new Retrofit.Builder()
                            .addConverterFactory(GsonConverterFactory.create())
                            .baseUrl("http://xxx.xxx")
                            .build();

        RockApi rockApi = retrofit.create(RockApi.class);

        Call<Movie> movie = rockApi.getMovie("max-age=" + 10);

        movie.enqueue(new Callback<Movie>() {
            @Override
            public void onResponse(Call<Movie> call, Response<Movie> response) {

            }

            @Override
            public void onFailure(Call<Movie> call, Throwable t) {

            }
        });
</pre>
- 如果我们想做的智能一些，我们可以在对Cache-Control进行赋值的时候，根据网络状态去赋不同的值
<code>
NetWorkUtil.isNetConnected(this) ? "max-age=6" : "only-if-cached, max-stale=" + 60 * 60 * 24 * 2;
</code>
