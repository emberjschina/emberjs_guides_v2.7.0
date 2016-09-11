_路由测试遵循在前面章节[Unit Testing Basics]介绍的单元测试和计算属性测试方法，因为Ember.Route继承至Ember.Object。_

[Ember Data]模型测试使用`moduleForModel`助手。

假设，有一个`Player`模型，它有两个属性`level`和`levelName`。我们想调用`levelUp()`方法去增加`level`值，并且当这个属性值达到5的时候设置`levelName`为一个新值。

> 创建模型使用命令`ember generate model player`。

```app/models/player.js
export default DS.Model.extend({
  level:     DS.attr('number', { defaultValue: 0 }),
  levelName: DS.attr('string', { defaultValue: 'Noob' }),

  levelUp() {
    var newLevel = this.incrementProperty('level');
    if (newLevel === 5) {
      this.set('levelName', 'Professional');
    }
  }
});
```

现在，我们创建测试，这个测试会调用`levelUp`方法，并且初始化`level`的值为4。使用`moduleForModel`实例化模型类：

```tests/unit/models/player-test.js
import { moduleForModel, test } from 'ember-qunit';
import Ember from 'ember';

moduleForModel('player', 'Unit | Model | player', {
  // Specify the other units that are required for this test.
  needs: []
});

test('should increment level when told to', function(assert) {
  // this.subject aliases the createRecord method on the model
  const player = this.subject({ level: 4 });

  // wrap asynchronous call in run loop
  Ember.run(() => player.levelUp());

  assert.equal(player.get('level'), 5, 'level gets incremented');
  assert.equal(player.get('levelName'), 'Professional', 'new level is called professional');
});
```

## 测试模型的关联关系

对于关联属性可能你只是想测试关系的设置是否是正确的。

假设一个`User`只能有一个`Profile`。

> 创建这两个模型可以用命令`ember generate model user`和`ember generate model profile`。

```app/models/profile.js
export default DS.Model.extend({
});
```

```app/models/user.js
export default DS.Model.extend({
  profile: DS.belongsTo('profile')
});
```

然后你可以这样测试这两个模型的关系是否是正确的。

```tests/unit/models/user-test.js
import { moduleForModel, test } from 'ember-qunit';
import Ember from 'ember';

moduleForModel('user', 'Unit | Model | user', {
  // Specify the other units that are required for this test.
  needs: ['model:profile']
});

test('should own a profile', function(assert) {
  const User = this.store().modelFor('user');
  const relationship = Ember.get(User, 'relationshipsByName').get('profile');

  assert.equal(relationship.key, 'profile', 'has relationship with profile');
  assert.equal(relationship.kind, 'belongsTo', 'kind of relationship is belongsTo');
});
```

_Ember Data contains extensive tests around the functionality of
relationships, so you probably don't need to duplicate those tests.  You could
look at the [Ember Data tests] for examples of deeper relationship testing if you
feel the need to do it._

_Ember Data包含了大量的关联关系测试，所以你不需要再去做重复的测试。如果有需要你可以查看[Ember Data tests]，它提供了更多更深层次的关联关系测试例子。_

[Ember Data]: https://github.com/emberjs/data
[Unit Testing Basics]: ../unit-testing-basics
[Ember Data tests]: https://github.com/emberjs/data/tree/master/tests
