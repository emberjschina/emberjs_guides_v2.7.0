组件测试使用`moduleForComponent`助手。

Let's assume we have a component with a `style` property that is updated
whenever the value of the `name` property changes. The `style` attribute of the
component is bound to its `style` property.

假设有一个组件，这个组件有一个属性`style`，属性值的更新依赖于一个普通属性`name`。`style`属性绑定到组件的`style`属性（`div`的`style`属性）上。

> 你可以使用命令`ember generate component pretty-color`生成一个组件。

```app/components/pretty-color.js
export default Ember.Component.extend({
  attributeBindings: ['style'],

  style: Ember.computed('name', function() {
    const name = this.get('name');
    return `color: ${name}`;
  })
});
```

```app/templates/components/pretty-color.hbs
Pretty Color: {{name}}
```

`moduleForComponent`会通过名字（`pretty-color`）查找组件和组件同名的组件模板（如果有）。请确定设置`integration: true`来启用集成测试。

```tests/integration/components/pretty-color-test.js
moduleForComponent('pretty-color', 'Integration | Component | pretty color', {
  integration: true
});
```

在方法`moduleForComponent`中指定的组件都会在组件执行`render()`方法的时候被调用，在应用中，我们可以通过创建模板实例的语法来调用`render()`方法。

我下面代码测试改变组件属性`name`的值来改变组件`style`属性并且使得HTML代码重新刷新。

```tests/integration/components/pretty-color-test.js
test('should change colors', function(assert) {
  assert.expect(2);

  // set the outer context to red
  this.set('colorValue', 'red');

  this.render(hbs`{{pretty-color name=colorValue}}`);

  assert.equal(this.$('div').attr('style'), 'color: red', 'starts as red');

  this.set('colorValue', 'blue');

  assert.equal(this.$('div').attr('style'), 'color: blue', 'updates to blue');
});
```

我们也可以测试这个组件，以确保它的模板内容是否被正确的渲染了：

```tests/integration/components/pretty-color-test.js
test('should be rendered with its color name', function(assert) {
  assert.expect(2);

  this.set('colorValue', 'orange');

  this.render(hbs`{{pretty-color name=colorValue}}`);

  assert.equal(this.$().text().trim(), 'Pretty Color: orange', 'text starts as orange');

  this.set('colorValue', 'green');

  assert.equal(this.$().text().trim(), 'Pretty Color: green', 'text switches to green');

});
```

### 用户交互测试

组件是自定义创建强大、交互、完备HTML元素的最好方式。更重要的是测试组件的方法和用户与组件的交互。

想象你有如下一个组件，当用户点击按钮的时候改变它的标题：


> 你可以用命令`ember generate component magic-title`创建组件。

```app/components/magic-title.js
export default Ember.Component.extend({
  title: 'Hello World',

  actions: {
    updateTitle() {
      this.set('title', 'This is Magic');
    }
  }
});
```

```app/templates/components/magic-title.hbs
<h2>{{title}}</h2>

<button {{action "updateTitle"}}>
  Update Title
</button>
```

jQuery触发器可以用与模拟用户交互已经测试点击按钮时更新标题的内容：

```tests/integration/components/magic-title-test.js
test('should update title on button click', function(assert) {
  assert.expect(2);

  this.render(hbs`{{magic-title}}`);

  assert.equal(this.$('h2').text(), 'Hello World', 'initial text is hello world');

  //Click on the button
  this.$('button').click();

  assert.equal(this.$('h2').text(), 'This is Magic', 'title changes after click');
});
```

### 测试Action

组件从Ember 2开始专门用于关闭action。关闭action允许组件后直接执行其他外部组件提供的方法。

> closure不知道怎么翻译，就直译为关闭了，也欢迎读者指正。

比如，假设有一个表单组件，当用户提交表单的时候这个组件执行了一个`submitComment`动作，并且传递表单的数据：

> 你可以使用命令`ember generate component comment-form`创建表单组件。

```app/components/comment-form.js
export default Ember.Component.extend({
  comment: '',

  actions: {
    submitComment() {
      this.get('submitComment')({ comment: this.get('comment') });
    }
  }
});
```

```app/templates/components/comment-form.hbs
<form {{action "submitComment" on="submit"}}>
  <label>Comment:</label>
  {{textarea value=comment}}

  <input type="submit" value="Submit"/>
</form>
```

下面的是测试代码，测试实例中，当用户双击按钮时触发组件的`submitComment`动作，进而触发断言指定的`externalAction`方法：

```tests/integration/components/comment-form-test.js
test('should trigger external action on form submit', function(assert) {

  // test double for the external action
  this.set('externalAction', (actual) => {
    let expected = { comment: 'You are not a wizard!' };
    assert.deepEqual(actual, expected, 'submitted value is passed to external action');
  });

  this.render(hbs`{{comment-form submitComment=(action externalAction)}}`);

  // fill out the form and force an onchange
  this.$('textarea').val('You are not a wizard!');
  this.$('textarea').change();

  // click the button to submit the form
  this.$('input').click();
});
```

### 服务测试

在实际情况中组件会依赖Ember的服务（`service`），这些服务可能是集成测试最底层的依赖。这些服务可以在组件中使用`register()`方法来注册。

> 你可以通过命令`ember generate component location-indicator`创建组件。

```app/components/location-indicator.js
export default Ember.Component.extend({
  locationService: Ember.inject.service('location-service'),

  // when the coordinates change, call the location service to get the current city and country
  city: Ember.computed('locationService.currentLocation', function () {
    return this.get('locationService').getCurrentCity();
  }),

  country: Ember.computed('locationService.currentLocation', function () {
    return this.get('locationService').getCurrentCountry();
  })
});
```

```app/templates/components/location-indicator.hbs
You currently are located in {{city}}, {{country}}
```

在上述代码中有个一个存根服务`locationService`，创建服务直接继承`Ember.Service`即可，然后在测试的`beforeEach()`方法中注册服务。在本测试中我们初始化为“New York”。

```tests/integration/components/location-indicator-test.js
import { moduleForComponent, test } from 'ember-qunit';
import hbs from 'htmlbars-inline-precompile';
import Ember from 'ember';

//Stub location service
const locationStub = Ember.Service.extend({
  city: 'New York',
  country: 'USA',
  currentLocation: {
    x: 1234,
    y: 5678
  },

  getCurrentCity() {
    return this.get('city');
  },
  getCurrentCountry() {
    return this.get('country');
  }
});

moduleForComponent('location-indicator', 'Integration | Component | location indicator', {
  integration: true,

  beforeEach: function () {
    this.register('service:location-service', locationStub);
    // Calling inject puts the service instance in the test's context,
    // making it accessible as "locationService" within each test
    this.inject.service('location-service', { as: 'locationService' });
  }
});
```

服务一旦注册了测试只需要从存根数据中检查出来，然后绑定到组件输出的服务中返回。

```tests/integration/components/location-indicator-test.js
test('should reveal current location', function(assert) {
  this.render(hbs`{{location-indicator}}`);
  assert.equal(this.$().text().trim(), 'You currently are located in New York, USA');
});
```

In the next example, we'll add another test that validates that the display changes
when we modify the values on the service.

在下面的例子中，我们会添加另一个测试例子用于验证当我们修改了服务值到时候显示的服务值是否改变。

```tests/integration/components/location-indicator-test.js
test('should change displayed location when current location changes', function (assert) {
  this.render(hbs`{{location-indicator}}`);
  assert.equal(this.$().text().trim(), 'You currently are located in New York, USA', 'origin location should display');
  this.set('locationService.city', 'Beijing');
  this.set('locationService.country', 'China');
  this.set('locationService.currentLocation', { x: 11111, y: 222222 });
  assert.equal(this.$().text().trim(), 'You currently are located in Beijing, China', 'location display should change');
});
```

### 等待异步行为

通常，与组件的交互会发生异步行为，比如HTTP请求、定时器。`wait`助手就是用于处理这种行为的，以确保Ajax请求或者定时器是否执行完成。

Imagine you have a typeahead component that uses [`Ember.run.debounce`](http://emberjs.com/api/classes/Ember.run.html#method_debounce)
to limit requests to the server, and you want to verify that results are displayed after typing a character.

假设，你有一个typeahead组件，此组件用[`Ember.run.debounce`](http://emberjs.com/api/classes/Ember.run.html#method_debounce)限制服务器请求，然后你想验证后再输入一个字符。

> 使用命令`ember generate component delayed-typeahead`创建组件。

```app/components/delayed-typeahead.js
export default Ember.Component.extend({
  actions: {
    handleTyping() {
      //the fetchResults function is passed into the component from its parent
      Ember.run.debounce(this, this.get('fetchResults'), this.get('searchValue'), 250);
    }
  }
});
```

```app/templates/components/delayed-typeahead.hbs
{{input value=searchValue key-up=(action 'handleTyping')}}
<ul>
{{#each results as |result|}}
  <li class="result">{{result.name}}</li>
{{/each}}
</ul>
```

在集成测试中，使用`wait`方法等待定时器启动和页面完成渲染。

```tests/integration/components/delayed-typeahead-test.js
import { moduleForComponent, test } from 'ember-qunit';
import wait from 'ember-test-helpers/wait';
import hbs from 'htmlbars-inline-precompile';

moduleForComponent('delayed-typeahead', 'Integration | Component | delayed typeahead', {
  integration: true
});

const stubResults = [
  { name: 'result 1' },
  { name: 'result 2' }
];

test('should render results after typing a term', function(assert) {
  assert.expect(2);

  this.set('results', []);
  this.set('fetchResults', (value) => {
    assert.equal(value, 'test', 'fetch closure action called with search value');
    this.set('results', stubResults);
  });

  this.render(hbs`{{delayed-typeahead fetchResults=fetchResults results=results}}`);
  this.$('input').val('test');
  this.$('input').trigger('keyup');

  return wait().then(() => {
    assert.equal(this.$('.result').length, 2, 'two results rendered');
  });

});
```
