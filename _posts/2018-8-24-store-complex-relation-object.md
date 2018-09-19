---
layout: post
title: '使用ObjectBox储存复杂对象'
subtitle: ''
date: 2018-08-24
categories: ObjectBox
cover: ''
tags: Android 数据库 ObjectBox
---
# 介绍
使用objectbox可以储存一些具有复杂关系的数据,比如一对多关系,比如一对多关系和多对多关系.

# 定义关系
比如我要储存这样关系的数据
![image](https://s1.ax1x.com/2018/08/24/P7zzoF.png)
MovieListContent对subjectmanager是一个一对一关系

subjectmanager对movielistsubject是一对多的关系

每一个subject对rating和image都是一对一的关系

# 设计实体类
层次由高到低是这样设计实体的
## MovieListContent
```Java
@Entity
public class MovieListContent {

    @Id
    @Expose
    private long databaseId;

    // 搜索结果的开始条目
    private String start;
    // 本此搜索的数目
    private String count;
    // 搜索的标题
    private String title;
    // 搜索的条目总数
    private String total;
    // 电影的条目
    @Transient
    private ArrayList<MovieListSubject> subjects;

    @Expose
    private ToOne<MovieListSubjectManager> movieListSubjectManagerToOne;
    // 省略getter和setter
}
```
## MovieListSubjectManager
```Java
@Entity
public class MovieListSubjectManager {

    @Id
    private long databaseId;

    private ToMany<MovieListSubject> movieListSubjectToMany;
}
```
## Rating
```Java
@Entity
public class Rating {

    @Id
    @Expose
    private long databaseId;

    private String max;
    private String average;
    private String stars;
    private String min;
}
```

## Image
```Java
@Entity
public class Image {

    @Id
    @Expose
    private long databaseId;

    private String small;

    private String large;

    private String medium;
}
```

# 储存
```Java
        final BoxStore boxStore = ((MyApplication) getApplication()).getBoxStore();
        final Box<MovieListContent> movieListContentBox = boxStore.boxFor(MovieListContent.class);
        final Box<MovieListSubject> movieListSubjectBox = boxStore.boxFor(MovieListSubject.class);
        final Box<Rating> ratingBox = boxStore.boxFor(Rating.class);
        final Box<MovieListSubjectManager> movieListSubjectManagerBox = boxStore.boxFor(MovieListSubjectManager.class);
        Box<Image> imageBox = boxStore.boxFor(Image.class);
        movieListContentBox.removeAll();
        movieListSubjectBox.removeAll();
        ratingBox.removeAll();
        imageBox.removeAll();
        movieListSubjectManagerBox.removeAll();
        DoubanApiService service = ApiServiceSington.getService();
        Observable<MovieListContent> observable = service.getMovieSearceContent("高圆圆", "0", "10");
        observable.subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<MovieListContent>() {
                    @Override
                    public void accept(MovieListContent movieListContent) throws Exception {
                        List<MovieListSubject> movieListSubjects = movieListContent.getSubjects();
                        MovieListSubjectManager movieListSubjectManager = new MovieListSubjectManager();
                        for (MovieListSubject subject : movieListSubjects) {
                            subject.getImageToOne().setTarget(subject.getImage());
                            subject.getRatingToOne().setTarget(subject.getRating());
                        }
                        movieListSubjectManager.getMovieListSubjectToMany().addAll(movieListSubjects);
                        movieListContent.getMovieListSubjectManagerToOne().setTarget(movieListSubjectManager);
                        movieListContentBox.put(movieListContent);
                    }
                });
```

# 从objectbox中获取对象
```Java
BoxStore boxStore = ((MyApplication) getApplication()).getBoxStore();
        Box<MovieListContent> movieListContentBox = boxStore.boxFor(MovieListContent.class);
        MovieListContent movieListContent = movieListContentBox.get(20);
        Log.d("tinuv_movieListContent", movieListContent.getTitle());
        MovieListSubjectManager manager = movieListContent.getMovieListSubjectManagerToOne().getTarget();
        for (int i = 0; i < 10; i++) {
            MovieListSubject movieListSubject = manager.getMovieListSubjectToMany().get(i);
            Log.d("tinuv_image", movieListSubject.getImageToOne().getTarget().getLarge());
            Log.d("tinuv_rating", movieListSubject.getRatingToOne().getTarget().getAverage());
            Log.d("tinuv_movielistsubject", movieListSubject.getTitle());
        }
```
# 总结
搞懂这些东西其实花了我很长时间(大概一天),因为英语不好,很多地方看文档看不懂,只能一遍一遍尝试,在尝试的过程中程序崩溃了很多次,我也崩溃了很多次,但是还是要感谢这个库的开发者,因为真的很好用也很有用.

# 参考
ObjectBox官方网站:https://docs.objectbox.io/