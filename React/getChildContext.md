# GetChildContext

> React有一个getChildContext方法用于设置组件内部参数，可以传给子组件，但需要注意，使用getChildComtext时需要使用childContextTypes设置类型才能使用


```
import React, {Component, PropTypes, contextTypes} from 'react';
import ReactDOM, { render } from 'react-dom'

class A extends Component {
    getChildContext() {
         return {
             name: "Jonas",
             fruit: "Banana"
         };
    }
    render() {
        return(
            <B />
        )
    }
}

A.childContextTypes = {
    name: React.PropTypes.string.isRequired,
    fruit: React.PropTypes.string.isRequired
}

class B extends Component {
    render() {
        return(
            <div>My favorite fruit is: {this.context.fruit}</div>
        )
    }
}

B.contextTypes =  {
    fruit: React.PropTypes.string.isRequired
}

// My favorite fruit is: Banana

render(<A />, document.body);

```