_路由测试遵循在前面章节[Unit Testing Basics]介绍的单元测试和计算属性测试方法，因为Ember.Route继承至Ember.Object。_

测试路由可以通过接受或单元测试来完成。对于路由验收测试可能会提供更好覆盖测试，因为路由通常是用来执行转换和加载数据，这两者都在上下文中相对来说比孤立的测试（单元测试）更好。

但是，有时单元测试对于路由来说是重要的。例如，我们希望有一个警报，从任何地方进入我们的应用程序都触发。警报方法`displayAlert`可以放入`ApplicationRoute`因为所有的行为和事件都会从子路由和子控制器冒泡到顶端的路由。

> 默认情况下，Ember CLI不会生成路由`application`。为了能继承路由的行为可以使用命令`ember generate route application`创建。Ember CLI不仅会生成路由文件还会生成同名的模板文件，所以执行命令的时候会询问是否覆盖`app/templates/application.hbs`，输入“n”即可。

```app/routes/application.js
export default Ember.Route.extend({
  actions: {
    displayAlert(text) {
      this._displayAlert(text);
    }
  },

  _displayAlert(text) {
    alert(text);
  }
});
```

在路由中[separated our concerns](http://en.wikipedia.org/wiki/Separation_of_concerns):
The action `displayAlert` contains the code that is called when the action is 
received, and the private function `_displayAlert` performs the work. While not 
necessarily obvious here because of the small size of the functions, separating 
code into smaller chunks (or "concerns"), allows it to be more readily isolated 
for testing, which in turn allows you to catch bugs more easily.

在这个路由中我们已经分开关注点：当方法`displayAlert`接受到动作哦时候就会执行，并且会执行私有函数`_displayAlter`。虽然不一定是明显的，因为小规模的功能，分离代码成更小的块（或“关注”），让它更容易被孤立的测试，这反过来又让你更容易捕捉错误。

下面是一个如何测试此路由的单元测试：

```tests/unit/routes/application-test.js
import { moduleFor, test } from 'ember-qunit';

let originalAlert;

moduleFor('route:application', 'Unit | Route | application', {
  beforeEach() {
    originalAlert = window.alert; // store a reference to window.alert
  },

  afterEach() {
    window.alert = originalAlert; // restore window.alert
  }
});

test('should display an alert', function(assert) {
  assert.expect(2);

  // with moduleFor, the subject returns an instance of the route
  let route = this.subject();

  // stub window.alert to perform a qunit test
  const expectedTextFoo = 'foo';
  window.alert = (text) => {
    assert.equal(text, expectedTextFoo, `expect alert to display ${expectedTextFoo}`);
  };

  // call the _displayAlert function which triggers the qunit test above
  route._displayAlert(expectedTextFoo);

  // set up a second stub to perform a test with the actual action
  const expectedTextBar = 'bar';
  window.alert = (text) => {
    assert.equal(text, expectedTextBar, `expected alert to display ${expectedTextBar}`);
  };
    
  // Now use the routes send method to test the actual action
  route.send('displayAlert', expectedTextBar);
});
```

[Unit Testing Basics]: ../unit-testing-basics
[separated our concerns]: http://en.wikipedia.org/wiki/Separation_of_concerns
