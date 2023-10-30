# RxJava

## Index

- [Something](#something)
- [Schedulers](#schedulers)
- [Map Operator](#map-operator)
- [Zip Operator](#zip-operator)
- [FlatMap with Filter Operator](#flatmap-with-filter-operator)
- [FlatMap with Zip Operator](#flatmap-with-zip-operator)
- [Subject](#subject)
- [Disposable](#disposable)
- [Backpressure](#backpressure)

## Something

- There are two main components of RxJava:

  <strong>Flowable, Observable, Single, and Completable</strong>: does some work and emit values.

  <strong>Observer</strong>: it gets the values.
  Observer get the emitted values from Observable by subscribing on it.

- Sample code:

  ```java
  // Observable just is a factory method that emmits the values.
  private Observable<String> getObservable() {
      return Observable.just("Cricket", "Football");
  }

  private Observer<String> getObserver() {
      return new Observer<String>() {

          @Override
          public void onSubscribe(Disposable d) {}

          @Override
          public void onNext(String value) {
              System.out.println(value);
          }

          @Override
          public void onError(Throwable e) {}

          @Override
          public void onComplete() {
              System.out.println("onComplete");
          }
      };
  }
  private void doSomeWork() {
      getObservable()
              // Run on a background thread
              .subscribeOn(Schedulers.io())
              // Be notified on the main thread
              .observeOn(AndroidSchedulers.mainThread())
              .subscribe(getObserver());
  }

  //result
  Cricket
  Football
  onComplete
  ```

**[⬆ Back to Index](#index)**

## Schedulers

<strong>RxJava provides some general use Schedulers:</strong>

- Schedulers.computation() : Used for CPU intensive tasks.
- Schedulers.io(): Used for IO bound tasks.
- Schedulers.from(Executor): Use with custom ExecutorService.
- Schedulers.newThread(): It always creates a new thread when a worker is needed. Since it’s not thread pooled and always creates a new thread instead of reusing one, this scheduler is not very useful.

**[⬆ Back to Index](#index)**

## Map Operator

- Map transforms the items emitted by an Observable by applying a function to each item.

- Example: Lets say we are getting ApiUser Object from api server then we are converting it into User Object because may be our database support User Not ApiUser Object. Here we are using Map Operator to do that.

  ```java
  private Observable<List<ApiUser>> getObservable() {
      return Observable.fromCallable(new Callable<List<ApiUser>>() {
          @Override
          public List<ApiUser> call() throws Exception {
              return Utils.getApiUserList();
          }
      });
  }

  private Observer<List<User>> getObserver() {
      return new Observer<List<User>>() {

          @Override
          public void onSubscribe(Disposable d) {

          }

          @Override
          public void onNext(List<User> userList) {
              // do anything with the list of user.
          }

          @Override
          public void onError(Throwable e) {

          }

          @Override
          public void onComplete() {

          }
      };
  }

  private void doSomeWork() {
      getObservable()
              // Run on a background thread
              .subscribeOn(Schedulers.io())
              // Be notified on the main thread
              .observeOn(AndroidSchedulers.mainThread())
              .map(new Function<List<ApiUser>, List<User>>() {

                  @Override
                  public List<User> apply(List<ApiUser> apiUsers) throws Exception {
                      return Utils.convertApiUserListToUserList(apiUsers);
                  }
              })
              .subscribe(getObserver());
  }
  ```

**[⬆ Back to Index](#index)**

## Zip Operator

- Zip combines the emissions of multiple Observables together via a specified function, then emits a single item for each combination based on the results of this function

- Example: Lets say we have to make two network call one for getting the list of users who loves cricket, another for users who loves football. And then returns the list of user who loves both. Here we are using Zip Operator to do that.

  ```java
  /*
   * This observable return the list of User who loves cricket
   */
  private Observable<List<User>> getCricketFansObservable() {
      return Rx2AndroidNetworking.get("url")
              .build()
              .getObjectListObservable(User.class);
  }

  /*
  * This observable return the list of User who loves Football
  */
  private Observable<List<User>> getFootballFansObservable() {
      return Rx2AndroidNetworking.get("url")
              .build()
              .getObjectListObservable(User.class);
  }

  /*
  *  Logic to filter user who loves both
  */
  private List<User> filterUserWhoLovesBoth(List<User> cricketFans, List<User> footballFans) {
      List<User> userWhoLovesBoth = new ArrayList<>();
      for (User cricketFan : cricketFans) {
          for (User footballFan : footballFans) {
              if (cricketFan.id == footballFan.id) {
                  userWhoLovesBoth.add(cricketFan);
              }
          }
      }
      return userWhoLovesBoth;
  }

  /*
  * This do the complete magic, make both network call
  * and then returns the list of user who loves both
  * Using zip operator to get both response at a time
  */
  private void findUsersWhoLovesBoth() {
      // here we are using zip operator to combine both request
      Observable.zip(getCricketFansObservable(), getFootballFansObservable(),
              new BiFunction<List<User>, List<User>, List<User>>() {
                  @Override
                  public List<User> apply(List<User> cricketFans, List<User> footballFans)
                          throws Exception {
                      List<User> userWhoLovesBoth =
                              filterUserWhoLovesBoth(cricketFans, footballFans);
                      return userWhoLovesBoth;
                  }
              })
              .subscribeOn(Schedulers.newThread())
              .observeOn(AndroidSchedulers.mainThread())
              .subscribe(new Observer<List<User>>() {
                  @Override
                  public void onSubscribe(Disposable d) {

                  }

                  @Override
                  public void onNext(List<User> users) {
                      // do anything with user who loves both
                  }

                  @Override
                  public void onError(Throwable e) {

                  }

                  @Override
                  public void onComplete() {

                  }
              });
  }
  ```

**[⬆ Back to Index](#index)**

## FlatMap with Filter Operator

- FlatMap transforms the items emitted by an Observable into Observables, then flattens the emissions from those into a single Observable.

- Filter emits only those items from an Observable that pass a predicate test.

- Example: Let say our server return the list of my friends , but we need to filter out only who those friends who is also following me. Here comes the filter operator to do so.

  ```java
  private Observable<List<User>> getAllMyFriendsObservable() {
      return Rx2AndroidNetworking.get("url")
              .build()
              .getObjectListObservable(User.class);
  }

  public void flatMapAndFilter() {
      getAllMyFriendsObservable()
              .flatMap(new Function<List<User>, ObservableSource<User>>() {
                  // flatMap - to return users one by one
                  @Override
                  public ObservableSource<User> apply(List<User> usersList) throws Exception {
                      return Observable.fromIterable(usersList);
                      // returning user one by one from usersList.
                  }
              })
              .filter(new Predicate<User>() {
                  @Override
                  public boolean test(User user) throws Exception {
                      // filtering user who follows me.
                      return user.isFollowing;
                  }
              })
              .subscribeOn(Schedulers.io())
              .observeOn(AndroidSchedulers.mainThread())
              .subscribe(new Observer<User>() {
                  @Override
                  public void onSubscribe(Disposable d) {

                  }

                  @Override
                  public void onNext(User user) {
                      // only the user who is following me comes here one by one
                  }

                  @Override
                  public void onError(Throwable e) {

                  }

                  @Override
                  public void onComplete() {

                  }
              });
  }
  ```

**[⬆ Back to Index](#index)**

## FlatMap with Zip Operator

- Zip combines the emissions of multiple Observables together via a specified function, then emits a single item for each combination based on the results of this function.

- Example: Let say our server return the list of the users , then we have to get the user detail of each corresponding user.

  ```java
  private Observable<List<User>> getUserListObservable() {
      return Rx2AndroidNetworking.get("url")
              .build()
              .getObjectListObservable(User.class);
  }

  private Observable<UserDetail> getUserDetailObservable(long id) {
      return Rx2AndroidNetworking.get("url")
              .build()
              .getObjectObservable(UserDetail.class);
  }

  public void flatMapWithZip(View view) {
      getUserListObservable()
              .flatMap(new Function<List<User>, ObservableSource<User>>() {
                  // flatMap - to return users one by one
                  @Override
                  public ObservableSource<User> apply(List<User> usersList) throws Exception {
                      return Observable.fromIterable(usersList);
                      // returning user one by one from usersList.
                  }
              })
              .flatMap(new Function<User, ObservableSource<Pair<UserDetail, User>>>() {
                  @Override
                  public ObservableSource<Pair<UserDetail, User>> apply(User user) throws Exception {
                      // here we get the user one by one and then we are zipping
                      // two observable - one getUserDetailObservable (network call to get userDetail)
                      // and another Observable.just(user) - just to emit user
                      return Observable.zip(getUserDetailObservable(user.id),
                              Observable.just(user),
                              new BiFunction<UserDetail, User, Pair<UserDetail, User>>() {
                                  @Override
                                  public Pair<UserDetail, User> apply(UserDetail userDetail, User user)
                                          throws Exception {
                                      // runs when network call completes
                                      // we get here userDetail for the corresponding user
                                      return new Pair<>(userDetail, user);
                                      // returning the pair(userDetail, user)
                                  }
                              });
                  }
              })
              .subscribeOn(Schedulers.io())
              .observeOn(AndroidSchedulers.mainThread())
              .subscribe(new Observer<Pair<UserDetail, User>>() {
                  @Override
                  public void onComplete() {
                      // do something onCompleted
                  }

                  @Override
                  public void onError(Throwable e) {
                      // handle error
                      Utils.logError(TAG, e);
                  }

                  @Override
                  public void onSubscribe(Disposable d) {

                  }

                  @Override
                  public void onNext(Pair<UserDetail, User> pair) {
                      // here we are getting the userDetail for the
                      // corresponding user one by one
                  }
              });
  }
  ```

**[⬆ Back to Index](#index)**

## Subject

<strong>Acts both as an observer and as an Observable</strong>

Let we have:

```java
private Observer<Integer> getFirstObserver() {
    return new Observer<Integer>() {

        @Override
        public void onSubscribe(Disposable d) {
            Log.d(TAG, " First onSubscribe : " + d.isDisposed());
        }

        @Override
        public void onNext(Integer value) {
            Log.d(TAG, " First onNext value : " + value);
        }

        @Override
        public void onError(Throwable e) {
            Log.d(TAG, " First onError : " + e.getMessage());
        }

        @Override
        public void onComplete() {
            Log.d(TAG, " First onComplete");
        }
    };
}

private Observer<Integer> getSecondObserver() {
    return new Observer<Integer>() {

        @Override
        public void onSubscribe(Disposable d) {
            Log.d(TAG, " Second onSubscribe : " + d.isDisposed());
        }

        @Override
        public void onNext(Integer value) {
            Log.d(TAG, " Second onNext value : " + value);
        }

        @Override
        public void onError(Throwable e) {
            Log.d(TAG, " Second onError : " + e.getMessage());
        }

        @Override
        public void onComplete() {
            Log.d(TAG, " Second onComplete");
        }
    };
}
```

### Publish Subject

- It emits all the subsequent items of the source Observable at the time of subscription.

- Here, if a student entered late into the classroom, he just wants to listen from that point of time when he entered the classroom. So, Publish will be the best for this use-case.
- Example:

  ```java
  PublishSubject<Integer> source = PublishSubject.create();

  // It will get 1, 2, 3, 4 and onComplete
  source.subscribe(getFirstObserver());

  source.onNext(1);
  source.onNext(2);
  source.onNext(3);

  // It will get 4 and onComplete for second observer also.
  source.subscribe(getSecondObserver());

  source.onNext(4);
  source.onComplete();

  //result
    First onSubscribe : false
      First onNext value : 1
      First onNext value : 2
      First onNext value : 3
      Second onSubscribe : false
      First onNext value : 4
      Second onNext value : 4
      First onComplete
      Second onComplete
  ```

### Replay Subject

- It emits all the items of the source Observable, regardless of when the subscriber subscribes.

- Here, if a student entered late into the classroom, he wants to listen from the beginning. So, here we will use Replay to achieve this.

- Example:

  ```java
  ReplaySubject<Integer> source = ReplaySubject.create();
  // It will get 1, 2, 3, 4
  source.subscribe(getFirstObserver());
  source.onNext(1);
  source.onNext(2);
  source.onNext(3);
  source.onNext(4);
  source.onComplete();
  // It will also get 1, 2, 3, 4 as we have used replay Subject
  source.subscribe(getSecondObserver());

  //result
    First onSubscribe : false
      First onNext value : 1
      First onNext value : 2
      First onNext value : 3
      First onNext value : 4
      First onComplete
      Second onSubscribe : false
      Second onNext value : 1
      Second onNext value : 2
      Second onNext value : 3
      Second onNext value : 4
      Second onComplete
  ```

### Behavior Subject

- It emits the most recently emitted item and all the subsequent items of the source Observable when an observer subscribes to it.

- Here, if a student entered late into the classroom, he wants to listen the most recent things(not from the beginning) being taught by the professor so that he gets the idea of the context. So, here we will use Behavior.

- Example:

  ```java
  BehaviorSubject<Integer> source = BehaviorSubject.create();
  // It will get 1, 2, 3, 4 and onComplete
  source.subscribe(getFirstObserver());
  source.onNext(1);
  source.onNext(2);
  source.onNext(3);
  // It will get 3(last emitted)and 4(subsequent item) and onComplete
  source.subscribe(getSecondObserver());
  source.onNext(4);
  source.onComplete();

  //result
    First onSubscribe : false
      First onNext value : 1
      First onNext value : 2
      First onNext value : 3
      Second onSubscribe : false
      Second onNext value : 3
      First onNext value : 4
      Second onNext value : 4
      First onComplete
      Second onComplete
  ```

### Async Subject

- It only emits the last value of the source Observable(and only the last value)

- Here, if a student entered at any point of time into the classroom, and he wants to listen only about the last thing(and only the last thing) being taught. So, here we will use Async.

- Example:

  ```java
  AsyncSubject<Integer> source = AsyncSubject.create();
  // It will get only 4 and onComplete
  source.subscribe(getFirstObserver());
  source.onNext(1);
  source.onNext(2);
  source.onNext(3);
  // It will also get only get 4 and onComplete
  source.subscribe(getSecondObserver());
  source.onNext(4);
  source.onComplete();

  //result
    First onSubscribe : false
      Second onSubscribe : false
      First onNext value : 4
      First onComplete
      Second onNext value : 4
      Second onComplete
  ```

**[⬆ Back to Index](#index)**

## Disposable

- clear() will clear all, but can accept new disposable.
- dispose() will clear all and set isDisposed = true, so it will not accept any new disposable.

**[⬆ Back to Index](#index)**

## Backpressure

In cases where a publisher is emitting items more rapidly than an operator or subscriber can consume them, then the items that are overflowing from the publisher need to be handled. If for example we try to maintain an ever-expanding buffer of items emitted by the faster publisher to eventually combine with items emitted by the slower one. This could result in OutOfMemoryError.

[For more information](https://mindorks.com/course/learn-rxjava/chapter/id/4/page/id/17)

**[⬆ Back to Index](#index)**
