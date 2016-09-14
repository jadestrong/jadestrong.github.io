---
title: React之Alt架构分析
date: 2016-09-12 21:58:53
tags: react、alt

---

## React之Alt架构分析

在使用Alt时，首先需要实例化一个alt实例，将其定义为一个单例模块，然后对其做一些必要的配置，然后就可以将它导出，当在项目中需要时可以执行引入，而不用每次都实例化了。
```
import Alt from 'alt';
import makeFinalStore from 'alt-utils/lib/makeFinalStore';

class Flux extends Alt {
	constructor(config) {
		super(config);
		this.FinalStore = makeFinalStore(this);
	}
}

const flux = new Flux();

export default flux;
```
在上面我们还使用了`alt-utils`的makeFinalStore,这个是用来监视所有的store都修改完毕之后，可以做最后处理。 ？

当定义好了alt实例，下面是需要将它配置到React项目中去，需要在Provider中配置，首先介绍下Provider的概念，这是Alt架构的两个概念之一，另一个是connect负责将action等连接到组件中去。至于Provider更多的是一个可扩展接口，其实可以将其直接嵌入到组件中，但是区分开来更加容易定制。
```
import React from 'react';
import AltContainer from 'alt-container';
import chromeDebug from 'alt-utils/lib/chromeDebug';
import alt from '../../libs/alt';
import setup from './setup';

setup(alt); //这个用来对alt做一些通用的配置

chromeDebug(alt); //这个是一个debug组件，结合chrome插件调试alt

React.Pref = require('react-addons-perf'); // 这个好像是性能分析工具

export default ({children}) =>
	<AltContainer flux={alt}>
		{children}
	</AltContainer>
```
在导出部分，使用了`AltContainer`，这个东西可以为子组件传入`this.props`属性信息，可以分开定义，也可以使用我们定义的`alt`。

在传入alt之前，我们需要将store绑定到alt中，这个操作就是在`setup.js`中做的：
```
import NoteStore from '../../stores/NoteStore';

export default alt => {
	alt.addStore('NoteStore', NoteStore);
}
```
这样就会将NoteStore通过alt实例，在AltContainer中传入{children}中，在组件中可以通过`this.props`来访问。

**注意：**此处在参数传递中还有困惑，即：是将NoteStore直接传入了组件的`props`属性，还是通过`connect`函数来做的？ 在后面看来这些都是在connect中做的处理。这一点在主组件文件`app.jsx`中也可以看，组件App是作为参数传入了connect函数，而最后导出的是connect函数最终执行的结果，即将修订后的App组件返回并在后续进行渲染

分析下connect函数所起的作用：
```
import React from 'react';

export default (state, actions) => {
	if (typeof state === 'function' || 
		(typeof state === 'object' && Object.keys(state).length)) {
		return target => connect(state, actions, target);
	}

	return target => props =>(
		<target {...Object.assign({}, props, actions)} />
	);
}

function connect(state = () => {}, actions = {}, target) {
	class Connect extends React.Component {
		constructor() {
			super();
			this.handleChange = this.handleChange.bind(this);
		}
		componentDidMount() {
			const {flux} = this.context;
			flux.FinalStore.listen(this.handleChange);
		}
		componentWillUnMount() {
			const {flux} = this.context;
			flux.FinalStore.unlisten(this.handleChange);
		}
		render() {
			const {flux} = this.context;
			const stores = flux.stores;
			const composedStores = composeStores(stores);

			return React.createElement(target, 
				{...Object.assign({}, this.props, state(composedStores), actions)});
		}
		handleChange() {
			this.forceUpdate();
		}
	}
	Connect.contextTypes = {
		flux: React.PropTypes.object.isRequired
	}
	return Connect;
}

function composeStores(stores) {
	let ret = {};
    
	Object.keys(stores).forEach(k => {
		const store = stores[k];
		ret = Object.assign({}, ret, store.getState());
	});

	return ret;
}
```

首先，这是一个高层函数，即它会根据参数的不同而返回另一个不同的函数，当参数state是一个函数或者对象时，会返回一个生成新组件的新函数，否则会返回一个函数，直接将传入的组件添加上属性进行返回。
新组件在渲染时会将传入的`React 类`使用`React.createElement`进行渲染处理，这其实是一层包装，用于为组件绑定响应的属性，如this.props、store、action等，当返回时就可以在组件中直接访问这些属性了。

流程：
`<Provider><App /></Provider>`(index.jsx) => 
`{childre} => <AltContainer flux={alt}> {children} </AltContainer>`(Provier.dev.jsx) => 
`export default connect(({notes}) => ({notes}), {NoteActions})(App);`(app.jsx) =>
`React.createElement(target, {...Object.assign({}, this.props, state(composedStores), actions)});`(connect.jsx) =>

首先是将App（此时的App其实是connect的返回，是一个包装之后的全新的组件）作为参数传入Provider，Provider通过AltContainer将alt传给connect返回的组件Connect，在Connect组件渲染时中可以通过`this.context`访问传入的alt，从中拿到flux，以及flux中的NoteStore,然后就可以将store和之前传入的action等绑定到该新创建的组件中。

至此，基本是React集成Alt的一个基本的过程，这些也就是对思路的一个基本梳理，后面还需要理解以及修补。
