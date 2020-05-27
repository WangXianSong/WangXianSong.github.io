---
layout: post
title:  "Android学习之MVP模式"
date:  2020-4-12 14:36:55
categories: Android
tags: MVP架构

---
* content
{:toc}

### 前言

在学习了 MVC 架构之后，发现 Activity 和 Fragment 和 XML 界面的开发就是典型的 MVC 架构模式，在 Activity 中不仅要处理 UI 操作，还要处理请求数据的操作。

然而在我接触到的开发项目中，看到一个 Activity 中的代码行数接近上千行是常态。在修改的过程中经常要在类里面不断的翻上翻下的，修改起来也非常费劲。

这样就导致了 MVC 架构模式耦合度太高、职责不明确，不易于维护的原因，这次就来学习 MVC 的演变出来的 MVP 架构模式。

> **MVC 的工作原理**：  MVC 即 Model View Controller，简单来说就是通过 Controller 的控制去操作 Model 层的数据，并且返回给 View 层展示。当用户触发事件的时候，View 层会发送指令到 Controller 层，接着 Controller 去通知 Model 层更新数据，Model 层更新完数据以后直接显示在 View 层上，这就是 MVC 的工作原理。

### MVP是什么

MVP 的全称是 Model-View-Presenter，MVP 是 MVC 的一种演进版本，将 MVC 中的 Controller 改为了 Presenter，View 通过接口与 Presenter 进行交互，有效降低 View（Activity / Fragment） 的复杂性，避免业务逻辑被塞进 View 中，使得 View 变得臃肿。

另外，MVP 模式会解除 View 与 Model 的耦合，同时又带来了良好的可扩展性、可测试性，保证了系统的整洁性、灵活性。虽然在简单的应用中可能会因为各种接口变得复杂，但在稍有规模的应用中，依然能保持结构的整洁和灵活。

### MVP的结构

*   `Model` 主要是提供数据的存取功能，Presenter 需要通过 Model 层存储和获取数据，Model 就像一个数据仓库。Model 是管理数据库和网络获取数据的角色。

*   `View` 一般是指 Activity 和 Fragmen t等，它含有一个 Presenter 成员变量。通常 View 需要实现一个逻辑接口，将 View 上的操作转交给 Presenter 进行实现，最后 Presenter 调用 View 的逻辑接口将结果返回给 View 元素。

*   `Presenter` 作为 View 与 Model 交互的中间纽带，处理与用户交互的负责逻辑。它从 Model 层检索数据后，返回给 View 层，使得 View 和 Model 之间没有耦合，也将业务逻辑从View角色上抽离出来。

![mvp.jpg](https://upload-images.jianshu.io/upload_images/6491732-9739ebb0f526d4fd.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


> 在通常开发中，`View`是指 Activity / Fragment，不过，Presenter 和 Activity 通过定义一个 view 接口进行关联，而 Presenter 和 Model 是通过 `Callback` 接口进行关联。
>
> *   View接口：显示提示框、数据更新；
> *   Callback接口：请求数据时反馈状态（成功、失败和异常等等）；

![mvp.png](https://upload-images.jianshu.io/upload_images/6491732-75174d0a10e844fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### MVP的优缺点

#### 优点

1.  分离了视图逻辑和业务逻辑，**降低了耦合**。

2.  Activity 只处理生命周期的任务，**代码变得更加简洁**。

3.  视图逻辑和业务逻辑分别抽象到了 View 和 Presenter 的接口中去，**提高代码的可阅读性**。

4.  Presenter 被抽象成接口，可以有多种具体的实现，所以**方便进行单元测试**。

5.  把业务逻辑抽到 Presenter 中去，**避免**后台线程引用着 Activity 导致 Activity 的资源无法被系统回收从而引起**内存泄露**。

6.  Presenter **代码可复用**，一个 Presenter 可以用于多个 View，而不需要更改 Presenter 的逻辑。

#### 缺点

1.  Presenter 中除了应用逻辑以外，还有大量的 View->Model，Model->View 的手动同步逻辑，造成 Presenter 比较笨重，维护起来会比较困难。

2.  由于对视图的渲染放在了 Presenter 中，所以视图和 Presenter 的交互会过于频繁。

3.  如果 Presenter 过多地渲染了视图，往往会使得它与特定的视图的联系过于紧密。一旦视图需要变更，那么 Presenter 也需要变更了。

4.  额外的代码复杂度及学习成本。

### MVP 和 MVC 的区别

*   MVP 是基于 MVC 模式上演变过来，与 MVC 有一定的相似性，Controller 和 Presenter 负责逻辑的处理，Model 提供数据，View 负责显示。

*   在 MVC 中，当 Model 被 Controller 更新后，**Model 会直接通知 View 并更新显示**。而在 MVP 中，**Model 与 View 不存在直接关系**，这两者之间间隔着的是 Presenter 层，其负责调控 View 与 Model 之间的间接交互，这也是 MVP 和 MVC 两者之间最大的区别。

*   此外 Presenter 与 View、Model 的交互使用接口定义交互操作可以**降低耦合**、简化代码。

### MVP 与 Activity、Fragment 的生命周期

> *   **问题原因**：由于 Model 在进行异步操作，例如请求网络数据，Presenter 持有 Activity 的强引用，如果在请求结束之前使得 Activity 被销毁了，那么由于网络请求还没有返回，导致 Presenter 持有 Activity 对象，使得 Activity 无法被回收，此时就会发生`内存泄露`。（也许应用中出现一次两次内存泄漏不会造成多大的影响，但应用在长时间使用后，若这些占据系统的大量内存的 Activity 得不到 GC 回收的话，最终会导致 `OOM` 的出现，就会直接 `Crash` 应用。）
>*   **解决办法**：通过`弱引用`和 Activity、Fragment 的生命周期来`绑定/解绑 View` 解决这个问题，建立 `BasePresenter`，是一个泛型类 ，泛型类型为 View 角色要实现的接口类型 。
> *   **好处**：Presenter 不需要在构造函数中传入 View 对象，而是在 View 中自由地通过Presenter 的 attachView 方法和 detachView 方法绑定和解绑 View 对象，除了 `attachView` 和 `detachView`，我们还可以另外声明 onResume 和 onStop 方法。



### Model层的单独优化

前面讲了 View 和 Presenter 两个层次，而 Model 层比较特殊，相对比较独立的存在，帮上层拿数据，这是因为 MVP 模式的理念就是让业务逻辑相互独立。但如果每个网络请求也独立成单个 Model 的话，代码操作起来也会非常麻烦，比如：

1. 无法对所有 Model 统一管理；
2. 每个 Model 对外提供的获取数据方法不一样，上层请求数据没有规范；
3. 代码冗余，重复性代码多；
4. 对已存在的 Model 进行修改繁琐；

那么就需要对 Model 进行封装优化，使得 Model 层变成一个庞大且独立单一的模块，请求方式规范化，管理直观化：

1. 数据请求能够单独编写和测试，无需配合上层界面测试；
2. 统一以 `DataModel` 类作为数据请求层的入口，通过反射机制获取对应的 Model；
3. 无缝切换不同的数据源（网络请求库、缓存、数据库）；



### MVP结构图

![MVP模式详细结构图](https://upload-images.jianshu.io/upload_images/6491732-b38838293978fa60.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 示例代码

- IBaseView：View接口中定义Activity的UI逻辑

  ```java
  public interface IBaseView {
      void showLoading();
      void hideLoading();
      void showToast(String msg);
      void showErr();
      Context getContext();
  }
  ```

  

- BaseActivity:主要是负责实现 BaseView 中通用的UI逻辑方法，如此这些通用的方法就不用每个Activity都要去实现一遍了。

  ```java
  public abstract class BaseActivity extends AppCompatActivity implements IBaseView {
      // 加载条
      private ProgressDialog mProgressDialog;
      // 获取Presenter实例，子类实现
      public abstract BasePresenter getPresenter();
      // 初始化Presenter的实例，子类实现
      public abstract void initPresenter();
      @Override
      protected void onCreate(@Nullable Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          // 初始化Presenter
          initPresenter();
          if (getPresenter()!=null){
              getPresenter().attachView(this);
          }
          // 初始化进度条
          mProgressDialog = new ProgressDialog(this);
          mProgressDialog.setCancelable(false);
      }
      @Override
      public void showLoading() {
          if (!mProgressDialog.isShowing()) {
              mProgressDialog.show();
          }
      }
      @Override
      public void hideLoading() {
          if (mProgressDialog.isShowing()) {
              mProgressDialog.dismiss();
          }
      }
      @Override
      public void showToast(String msg) {
          Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
      }
      @Override
      public void showErr() {
          showToast("err....");
      }
      @Override
      public Context getContext() {
          return BaseActivity.this;
      }
      @Override
      protected void onDestroy() {
          super.onDestroy();
          if (getPresenter() != null){
              getPresenter().detachView();
          }
      }
  }
  ```

  

- MainActivity：继承了BaseActivity抽象类，实现了getPresenter和initPresenter完成P层绑定。实现IMvpView接口中的showData达到UI更新操作。

  ```java
  public class Main4Activity extends BaseActivity implements IMvpView {
      TextView mTextView;
      MvpPresenter mvpPresenter;
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          setContentView(R.layout.activity_main3);
          mTextView = findViewById(R.id.text);
      }
      @Override
      public BasePresenter getPresenter() {
          return mvpPresenter;
      }
      @Override
      public void initPresenter() {
          mvpPresenter = new MvpPresenter(); //初始化Presenter
          // mvpPresenter.attachView(this);// attachView放在抽象父类中
      }
      // 按钮1
      public void getData(View view) {
          mvpPresenter.getData("normal");
      }
      // 按钮2
      public void getDataForFailure(View view) {
          mvpPresenter.getData("failure");
      }
      // 按钮3
      public void getDataForError(View view) {
          mvpPresenter.getData("error");
      }
      @Override
      public void showData(String data) {
          mTextView.setText(data);
      }
  }
  ```

  

- IMvpView：IMvpView是Activity与Presenter层的中间层，它的作用是根据具体业务的需要，为Presenter提供调用Activity中具体UI逻辑操作的方法。

  ```java
  /**
   * View接口是Activity与Presenter层的中间层，它的作用是根据具体业务的需要，
   * 为Presenter提供调用Activity中具体UI逻辑操作的方法。
   */
  public interface IMvpView extends IBaseView {
      /**
       * 当数据请求成功后，调用此接口显示数据
       * @param data 数据源
       */
      void showData(String data);
  }
  ```

  

- BasePresenter：处理View的生命周期；

  ```java
  public class BasePresenter<V extends IBaseView> {
      // 绑定的view
      private V mView;
      // 绑定view，一般在初始化中调用该方法
      public void attachView(V mvpView) {
          this.mView = mvpView;
      }
      //  断开view，一般在onDestroy中调用
      public void detachView() {
          this.mView = null;
      }
      // 是否与View建立连接，每次请求业务之前都要判断
      public boolean isViewAttached() {
          return mView != null;
      }
      // 获取当前连接的view
      public V getmView() {
          return mView;
      }
  }
  ```

  

- MvpPresenter：该类是具体的逻辑业务处理类，负责请求数据，并对数据请求的反馈进行处理。

  ```java
  public class MvpPresenter extends BasePresenter<IMvpView> {
      /**
       * 获取网络数据
       * @param userId
       */
      public void getData(String userId) {
          if (!isViewAttached()) {
              return;
          }
          //显示进度条
          getmView().showLoading();
          DataModel.request1(UserDataModel.class)
                  .params(userId).execute(new MvpCallback() {
              @Override
              public void onSuccess(Object data) {
                  //调用view接口显示数据
                  if (isViewAttached()) {
                      getmView().showData((String) data);
                  }
              }
              @Override
              public void onFailure(String msg) {
                  if (isViewAttached()) {
                      getmView().showToast(msg);
                  }
              }
              @Override
              public void onError() {
                  if (isViewAttached()) {
                      getmView().showErr();
                  }
              }
              @Override
              public void onComplete() {
                  if (isViewAttached()) {
                      getmView().hideLoading();
                  }
              }
          });
      }
  }
  ```

  

- MvpCallback

  ```java
  /**
   * Callback 接口是Model层给Presenter层反馈请求信息的传递载体，
   * 所以需要在Callback中定义数据请求的各种反馈状态
   * 除了请求成功的回调方法，其他的像请求失败，请求出错这些方法我们做的事几乎是一样的。
   * 后期可以构建一个通用的BaseCallBack去处理请求的异常情况
   */
  public interface MvpCallback<T> {
      /**
       * 数据请求成功
       * @param data 请求到的数据
       */
      void onSuccess(T data);
      /**
       * 网络返回数据失败，请求成功
       * @param msg 无法正常返回数据的原因
       */
      void onFailure(String msg);
      /**
       * 请求数据失败、无法联网、缺少权限、内存泄露等等原因
       */
      void onError();
      /**
       * 无论执行上面那个方法，最后都会执行此方法，可以在这里设置隐藏加载框
       */
      void onComplete();
  }
  ```

- DataModel：通过反射机制获取对应的model

  ```java
  public class DataModel {
      public static <T extends BaseModel> T request1(Class<T> cls) {
          // 声明一个空的BaseModel
          T model = null;
          try {
              //利用反射机制获得对应Model对象的引用
              model = (T) cls.newInstance();
          } catch (InstantiationException e) {
              e.printStackTrace();
          } catch (IllegalAccessException e) {
              e.printStackTrace();
          }
          return model;
      }
  }
  ```

  

- BaseModel：定义了对外的请求数据规则，包括设置参数的方法和设置Callback的方法，还可以定义一些通用的数据请求方法，比如说网络请求的Get和Post方法。

  ```java
  public abstract class BaseModel<T> {
      //数据请求参数
      protected String[] mParams;
      /**
       * 设置数据请求参数
       * @param args 参数数组
       */
      public BaseModel params(String... args) {
          mParams = args;
          return this;
      }
      // 添加Callback并执行数据请求
      // 具体的数据请求由子类实现
      public abstract void execute(MvpCallback<T> callback);
      // 执行Get网络请求，此类看需求由自己选择写与不写
      protected void requestGetAPI(String url, MvpCallback<T> callback) {
          //这里写具体的网络请求
      }
      // 执行Post网络请求，此类看需求由自己选择写与不写
      protected void requestPostAPI(String url, Map params, 			 MvpCallback<T> callback) {
          //这里写具体的网络请求
      }
  }
  ```

  

- getNetData：实现具体的Model请求时必须要重写BaseModel的抽象方法execute
  
  ```java
  public class UserDataModel extends BaseModel<String> {
      @Override
      public void execute(final MvpCallback<String> callback) {
          new Handler().postDelayed(new Runnable() {
              @Override
              public void run() {
                  // mParams 是从父类得到的请求参数
                  switch (mParams[0]){
                      case "normal":
                          callback.onSuccess("根据参数"+mParams[0]+"的请求网络数据成功");
                          break;
                      case "failure":
                          callback.onFailure("请求失败：参数有误");
                          break;
                      case "error":
                          callback.onError();
                          break;
                  }
                  callback.onComplete();
              }
          },2000);
      }
  }
  ```

### 效果图

![MVP效果图](https://upload-images.jianshu.io/upload_images/6491732-367a77eeade904e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 参考文章

*   [https://www.jianshu.com/p/4e5c1fd007bf](https://www.jianshu.com/p/4e5c1fd007bf)
*   https://www.jianshu.com/p/d24f9856f97d

