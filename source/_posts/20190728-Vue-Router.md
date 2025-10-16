---
title: Vue Router
date: 2019-07-28 17:17:17
tags:
  - vue
categories: Vue
---
## Query parameters

`https://something.com/user/456?locale=en`

```vue
<router-link 
	:to="{ name: 'pathName', 
    	   params: { id: $route.params.id }, 
           query: { locale: 'en' } }">
</router-link>
```

## Adding hash fragment

```vue
<router-link :to="{ name: pathName, hash: '#text' }">
	Jump to content
</router-link>
```

## Scroll behavior

```js
import { routes } from './routes.js'
const router = new VueRouter({
	routes,
    scrollBehavior(to, from, savedPosition) {
    	if (savedPosition) {
        	return savedPosition;
        }
        if (to.hash) {
        	return { selector: to.hash };
        }
        return { x: 0, y: 0 };
    }
});
```

Here, **routes** are all our routes kept in a separate file. The `scrollBehavior()`function is what manages the scrolling of our routes. It has 3 parameters:

1. *to* — This represents the new route we will be visiting
2. *from* — This is the previous route we came from. So if we click on a `<router-link>` on Home page to visit the About page, then *to* would be our About page and *from* is the Home page.
3. *savedPosition* — This is the important parameter. It represents the previous position before scrolling. It will become clear after I explain what the above function does.

## 参考：

https://medium.com/@NAPOLEON039/the-lesser-known-amazing-things-vuerouter-can-do-3fbb2c191c00

