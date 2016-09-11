_控制器测试遵循在前面章节[Unit Testing Basics]介绍的单元测试和计算属性测试方法，因为Ember.Controller继承至Ember.Object。_

对于ember-qunit框架来说使用测试助手来测试控制器是非常简单的。

### 测试控制器动作

下面的例子中，有一个包含两个属性，一个设置属性的方法和一个名为`setProps`动作的控制器`PostsController`。

> 你可以使用命令`ember generate controller posts`创建控制器。

```app/controllers/posts.js
export default Ember.Controller.extend({
  propA: 'You need to write tests',
  propB: 'And write one for me too',

  setPropB(str) {
    this.set('propB', str);
  },

  actions: {
    setProps(str) {
      this.set('propA', 'Testing is cool');
      this.setPropB(str);
    }
  }
});
```

`setProps`方法直接设置属性`propA`的值，然后调用方法`setPropB()`设置另一个属性值。在生成的测试中，Ember-cli通常用于生成`moduleFor`助手来启动容器的测试：

```tests/unit/controllers/posts-test.js
moduleFor('controller:posts', {
});
```

接下来，我们用`this.subject()`获取控制器`PostsController`的实例，然后写一个测试来检查动作。`this.subject()`是一个测试助手，此测试助手来至`ember-qunit`库，并且返回的是从`moduleFor`启动的单例。

```tests/unit/controllers/posts-test.js
test('should update A and B on setProps action', function(assert) {
  assert.expect(4);

  // get the controller instance
  const ctrl = this.subject();

  // check the properties before the action is triggered
  assert.equal(ctrl.get('propA'), 'You need to write tests', 'propA initialized');
  assert.equal(ctrl.get('propB'), 'And write one for me too', 'propB initialized');

  // trigger the action on the controller by using the `send` method,
  // passing in any params that our action may be expecting
  ctrl.send('setProps', 'Testing Rocks!');

  // finally we assert that our values have been updated
  // by triggering our action.
  assert.equal(ctrl.get('propA'), 'Testing is cool', 'propA updated');
  assert.equal(ctrl.get('propB'), 'Testing Rocks!', 'propB updated');
});
```

### 嵌套控制器测试

有时候，一个控制器内部还可能依赖另一个控制器。这情况是通过一个控制器注入到另一个实现的。例如，有两个简单的控制器。控制器`CommentsController`通过注入（`inject`）方式注入到控制器`PostController`中：

> 执行命令`ember generate controller post`和`ember generate controller comments`生成控制器。

```app/controllers/post.js
export default Ember.Controller.extend({
  title: Ember.computed.alias('model.title')
});
```

```app/controllers/comments.js
export default Ember.Controller.extend({
  post: Ember.inject.controller(),
  title: Ember.computed.alias('post.title')
});
```

这种情况下，我们需要在方法`moduleFor`中通过第三个可选的参数来启动这2个控制器。

```tests/unit/controllers/comments-test.js
moduleFor('controller:comments', 'Comments Controller', {
  needs: ['controller:post']
});
```

现在写一个测试示例，在控制器`PostController`设置`post`模型的属性，这个属性在控制器`CommentsController`中也是可以使用的。

```tests/unit/controllers/comments-test.js
test('should modify the post model', function(assert) {
  assert.expect(2);

  // grab an instance of `CommentsController` and `PostController`
  const ctrl = this.subject();
  const postCtrl = ctrl.get('post');

  // wrap the test in the run loop because we are dealing with async functions
  Ember.run(function() {

    // set a generic model on the post controller
    postCtrl.set('model', Ember.Object.create({ title: 'foo' }));

    // check the values before we modify the post
    assert.equal(ctrl.get('title'), 'foo', 'title is set');

    // modify the title of the post
    postCtrl.get('model').set('title', 'bar');

    // assert that the controllers title has changed
    assert.equal(ctrl.get('title'), 'bar', 'title is updated');
  });
});
```

[Unit Testing Basics]: ../unit-testing-basics
[needs]: ../../controllers/dependencies-between-controllers
