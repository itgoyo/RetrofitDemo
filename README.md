# RetrofitDemo
一个使用Retrofit+Glide+Recycleview练手项目
---

### loopback.io

搭建本地服务
npm install -g loopback-cli

Retrofit [github地址](https://github.com/square/retrofit)

在AndroidStudio中引入

Download the latest JAR or grab via Maven:
```java
<dependency>
  <groupId>com.squareup.retrofit2</groupId>
  <artifactId>retrofit</artifactId>
  <version>2.2.0</version>
</dependency>
```
or Gradle:
```java
compile 'com.squareup.retrofit2:retrofit:2.2.0'
```

------
同时在项目中使用Butterknife

### Download

```
dependencies {
  compile 'com.jakewharton:butterknife:8.5.1'
  annotationProcessor 'com.jakewharton:butterknife-compiler:8.5.1'
}
```

Snapshots of the development version are available in Sonatype's snapshots repository.

Library projects

To use Butter Knife in a library, add the plugin to your buildscript:

```
buildscript {
  repositories {
    mavenCentral()
   }
  dependencies {
    classpath 'com.jakewharton:butterknife-gradle-plugin:8.5.1'
  }
}
```
and then apply it in your module:

```
apply plugin: 'com.android.library'
apply plugin: 'com.jakewharton.butterknife
```
-------

使用步骤

* 1
```java
public  Interface Service{
    @GET("/")
    Call<String> getXXX();
} 
```

* 2
```java
new Retrofit.Builder.baseUrl("你请求的网站地址（必须以"/结尾"）")
.addConverterFactory(new Converter.Factory() {
                    @Override
                    public Converter<ResponseBody, ?> responseBodyConverter(Type type,Annotation[] annotations, Retrofit retrofit) {
                        return new Converter<ResponseBody, String>() {
                            @Override
                            public String convert(ResponseBody value) throws IOException {
                                return value.string();
                            }
                        };
                    }
                }).build();
```
如果请求返回是比较复杂的json数据格式

先添加复杂数据的解析依赖包，使用注解
```
 compile 'com.squareup.retrofit2:converter-gson:2.2.0'
```

```
.addConverterFactory(GsonConverterFactory.create())
```
举个简单例子
```
 Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://www.tngou.net")
                .addConverterFactory(GsonConverterFactory.create()).build();
        CookService cookService = retrofit.create(CookService.class);
        Call<TianGouBean> call = cookService.getTianGou(0, 1, 20);
        call.enqueue(this);
```

Retorfit在请求数据的时候，如果地址只是个别名称不一样的话，可以直接封装成一个方法方便调用
```
@GET("/api/{category}/list")
Call<Tngou> getList
(@Path(category) String category, @Query("id") int id,@Query("page") int page,@Query("raws")int raws);
```

如果想要图片的样式变得好看的可以使用**CardView**
```
compile 'com.android.support:cardview-v7:25.1.1'
```
自带缓存效果的网络请求框架**Glide**
```
compile 'com.github.bumptech.glide:glide:3.7.0'
```
由于Glide本身已封装好了方法，可以是图片直接set在ImageView上
```
Glide.with(mContext).load(URL).into(mImageView);
```
如果你想让图片先转换成Bitmap对象的话可以使用一下方法：
```
 Glide.with(mContext).load(URL+tngouBean.getImg()).asBitmap().diskCacheStrategy(DiskCacheStrategy.SOURCE).into(new SimpleTarget<Bitmap>() {
  @Override
  public void onResourceReady(Bitmap resource, GlideAnimation<? super Bitmap> glideAnimation) {
                    mImageView.setImageBitmap(resource);
                }
            });
```
生成一个bitmap对象
```
new SimpleTarget<Bitmap>() {
@Override
public void onResourceReady(Bitmap resource, GlideAnimation<? super Bitmap> glideAnimation) {
                    mImageView.setImageBitmap(resource);
                }
            }
```
在使用RecycleView的时候要先设置好图片显示的属性，否则无法正常显示
```
//垂直布局
LinearLayoutManager layoutManager = new LinearLayoutManager(getApplicationContext());
layoutManager.setOrientation(LinearLayoutManager.VERTICAL);
```
```
//瀑布流布局
StaggeredGridLayoutManager staggeredGridLayoutManager = new StaggeredGridLayoutManager(2,VERTICAL);
```
最后RecycleView在设置显示方式
```
mRecycleview.setLayoutManager(staggeredGridLayoutManager);
```

如果获取到的json数据需要存到List时
```
JSONArray jarray = courseinfojson.getJSONArray("FileInfo");
    FileInfo_list = new ArrayList<FileInfo>();
    for (int i = 0; i < jarray.length(); i++) {
        JSONObject mFileInfo = jarray.getJSONObject(i);
        FileInfo fileinfo = mGson.fromJson(mFileInfo.toString(), FileInfo.class);
        FileInfo_list.add(fileinfo);
     }
```

如果是Bean类型
```
 List<TianGouBean.TngouBean> tngou = response.body().getTngou();
        int size = tngou.size();
        for (int i = 0; i <size ; i++) {
            mList.add(tngou.get(i));
        }
```
项目效果图：

![](http://omvbl46i3.bkt.clouddn.com/17-5-11/81427777-file_1494492528075_164ec.jpg)

