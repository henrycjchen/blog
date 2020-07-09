# mixins 很糟糕

mixins 不必要，而且是有问题的。



## mixins 引入隐式依赖

组件可以执行 mixin  里的方法，mixin 反过来也可以执行组件里的方法，这让新开发者很难清晰地读出他们之间的依赖关系。

当我们想修改当前组件的某个 state 键名或方法名时，会自然地只搜索当前组件文件并替换，但如果这个组件里用了 mixin，指不定哪个 mixin 就依赖着我们要修改的这个键名/方法名，这时你还得修改 mixin，很让人挠头发。

mixin 带来的隐式依赖让团队的新成员很难快速融入到项目中。当组件中出现了一个不存在的方法，在使用了 mixin 的情况下，你都不知道这个方法到底定义在哪个 mixin 下，只能一个个去找，如果 mixin 中还有 mixin，那。。。。。。

如果 mixin 互相依赖，由于不像组件是层层嵌套关系，你一时半会也很难理清数据的执行链路。



## mixins 会导致命名冲突

react 不允许组件与 mixin 有方法名冲突，会直接报错：

> ReactClassInterface: You are attempting to define `setInterval` on your component more than once. This conflict may be due to a mixin.

（这点与 vue 有些许不同，在 vue 中，如果是生命钩子重复，会一个个依次执行，如果是普通函数、变量，则 mixin 会被组件的函数覆盖）

当方法名冲突时，修改组件方法的名字还是 mixin 方法的名字，都能让人纠结好一阵。



## mixins 引发复杂度滚球效应

即使 mixins 一开始很简单，也会随着时间变得越来越复杂。



## mixins 迁移

mixin 并不会被弃用，在 `React.createClass` 里依然可以使用 mixin，只是 es6 class 创建的组件已经不支持 mixin



### 高阶组件

高阶组件将原本平级的 mixins 转换成具有层级关系的结构，让组件回归数据从上向下流的原始状态，更利于 debug

使用 mixin 时：

```js
var SubscriptionMixin = {
  getInitialState: function() {
    return {
      comments: DataSource.getComments()
    };
  },

  componentDidMount: function() {
    DataSource.addChangeListener(this.handleChange);
  },

  componentWillUnmount: function() {
    DataSource.removeChangeListener(this.handleChange);
  },

  handleChange: function() {
    this.setState({
      comments: DataSource.getComments()
    });
  }
};

var CommentList = React.createClass({
  mixins: [SubscriptionMixin],

  render: function() {
    // Reading comments from state managed by mixin.
    var comments = this.state.comments;
    return (
      <div>
        {comments.map(function(comment) {
          return <Comment comment={comment} key={comment.id} />
        })}
      </div>
    )
  }
});

module.exports = CommentList;

```

使用高阶组件时：

```js
// This function takes a component...
function withSubscription(WrappedComponent) {
  // ...and returns another component...
  return React.createClass({
    getInitialState: function() {
      return {
        comments: DataSource.getComments()
      };
    },

    componentDidMount: function() {
      // ... that takes care of the subscription...
      DataSource.addChangeListener(this.handleChange);
    },

    componentWillUnmount: function() {
      DataSource.removeChangeListener(this.handleChange);
    },

    handleChange: function() {
      this.setState({
        comments: DataSource.getComments()
      });
    },

    render: function() {
      // ... and renders the wrapped component with the fresh data!
      return <WrappedComponent comments={this.state.comments} />;
    }
  });
}
```



### 渲染逻辑

组件调用 mixin 的方法，mixin 调用组件的方法，现实版的莫比乌斯环

```js
var RowMixin = {
  // Called by components from render()
  renderHeader: function() {
    return (
      <div className='row-header'>
        <h1>
          {this.getHeaderText() /* Defined by components */}
        </h1>
      </div>
    );
  }
};

var UserRow = React.createClass({
  mixins: [RowMixin],

  // Called by RowMixin.renderHeader()
  getHeaderText: function() {
    return this.props.user.fullName;
  },

  render: function() {
    return (
      <div>
        {this.renderHeader() /* Defined by RowMixin */}
        <h2>{this.props.user.biography}</h2>
      </div>
    )
  }
});
```

这种情况应该直接将 mixin 转换成组件

```js
function RowHeader(props) {
  return (
    <div className='row-header'>
      <h1>{props.text}</h1>
    </div>
  );
}

function UserRow(props) {
  return (
    <div>
      <RowHeader text={props.user.fullName} />
      <h2>{props.user.biography}</h2>
    </div>
  );
}
```



### 上下文

使用 mixins 封闭对 context 的调用：

```js
var RouterMixin = {
  contextTypes: {
    router: React.PropTypes.object.isRequired
  },

  // The mixin provides a method so that components
  // don't have to use the context API directly.
  push: function(path) {
    this.context.router.push(path)
  }
};

var Link = React.createClass({
  mixins: [RouterMixin],

  handleClick: function(e) {
    e.stopPropagation();

    // This method is defined in RouterMixin.
    this.push(this.props.to);
  },

  render: function() {
    return (
      <a onClick={this.handleClick}>
        {this.props.children}
      </a>
    );
  }
});

module.exports = Link;
```

这种情况下，建议用高阶组件替代：

```js
function withRouter(WrappedComponent) {
  return React.createClass({
    contextTypes: {
      router: React.PropTypes.object.isRequired
    },

    render: function() {
      // The wrapper component reads something from the context
      // and passes it down as a prop to the wrapped component.
      var router = this.context.router;
      return <WrappedComponent {...this.props} router={router} />;
    }
  });
};

var Link = React.createClass({
  handleClick: function(e) {
    e.stopPropagation();

    // The wrapped component uses props instead of context.
    this.props.router.push(this.props.to);
  },

  render: function() {
    return (
      <a onClick={this.handleClick}>
        {this.props.children}
      </a>
    );
  }
});

// Don't forget to wrap the component!
module.exports = withRouter(Link);
```

如果你用的第三方库就是个 mixin，你应该给他们提 issue，让他们提供一个高阶组件版本，或者你也可以自己把 mixin 封装成高阶组件。



### 公共函数

更常见的，mixin 是用于为组件提供公共函数

```js
var ColorMixin = {
  getLuminance(color) {
    var c = parseInt(color, 16);
    var r = (c & 0xFF0000) >> 16;
    var g = (c & 0x00FF00) >> 8;
    var b = (c & 0x0000FF);
    return (0.299 * r + 0.587 * g + 0.114 * b);
  }
};

var Button = React.createClass({
  mixins: [ColorMixin],

  render: function() {
    var theme = this.getLuminance(this.props.color) > 160 ? 'dark' : 'light';
    return (
      <div className={theme}>
        {this.props.children}
      </div>
    )
  }
});
```

这种情况，应该直接以 js 包的形式使用

```js
var getLuminance = require('../utils/getLuminance');

var Button = React.createClass({
  render: function() {
    var theme = getLuminance(this.props.color) > 160 ? 'dark' : 'light';
    return (
      <div className={theme}>
        {this.props.children}
      </div>
    )
  }
});
```

### 其他用例

有时需要在每个生命周期中输出日志，针对这种情况，我们会在未来提供官方的 DevTools API 来解决这个问题，但这也需要很大的工作量。在此之前，如果你只能用 mixin log 一段时间了。

除此之外，上面提到的组件化、高阶组件和工具包形式，将能很大程度地避免使用 mixin



**参考资料**

1. Mixins Considered Harmful, [Dan Abramov](https://twitter.com/dan_abramov), 2016-07-13, https://reactjs.org/blog/2016/07/13/mixins-considered-harmful.html