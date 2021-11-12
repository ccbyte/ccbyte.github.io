---
title: '在vue3中实现mapAction和mapGetters功能'
date: 2021-10-12T18:29:00
tags: ["Vue.js", "JavaScript"]
---
# vue2
mixin.js
```js
import { mapGetters, mapActions } from 'vuex'
export const storeMixin = {
    computed: {
        ...mapGetters([
            'dataA',
            'dataB'
        ])
    },
    methods: {
        ...mapActions([
            'setDataA',
            'setDataB',
        ]),
    }
}
return storeMixin
```
页面中使用
```js
import mixin from 'mixin.js'
export default {
    mixins: [mixin],
    data () {
        return {}
    },
    methods: {
        func() {}
    }
}
```
# vue3
使用vue3 Composition API可以避免vue2 mixins混入的重名问题。

但是由于vue3的setup()中没有this指向vue实例，所以需要useStore()方法来获取store，同时1.使用以下方法封装一个mapGetters 2.在store._actions中可以获取到所有的action并解构出来。

工具类中封装
```js
/**
 * 在composition api中代替mapGetters
 * @param {Object} 如store.getters
 * @returns 包含所有getter，解构使用
 */
export const useGetters = (getters) => {
    return Object.fromEntries(
        Object.keys(getters).map(
            getter => [getter, computed(() => getters[getter])]
        )
    )
}
```
useSomeEffect.js
```js
import { useGetters } from 'util'
export default function () {
    const store = useStore()
    let { // getters
        dataA,
        dataB,
    } = useGetters(store.getters)
    const { // actions
        setDataA: [setDataA],
        setDataB: [setDataB],
    } = store._actions
    return {
        dataA, setDataA,
        dataB, setDataB
    }
}
```
页面中使用
```js
import useSomeEffect from 'useSomeEffect'
let { dataB, setDataB } = useSomeEffect() // 想要啥用啥
```

> 参考资料：
> - [mapState, mapGetters, mapMutations and mapActions with Vue3/Vuex4 and \<script setup\>](https://medium.com/geekculture/mapgetters-with-vue3-vuex4-and-script-setup-5827f83930b4)
> - [use `mapActions` or `mapGetters` with Vuex 4 and Vue 3](https://stackoverflow.com/questions/63216740/use-mapactions-or-mapgetters-with-vuex-4-and-vue-3)
> - [vue3.x实践经验-1、mapState、mapGetters、mapMutations、mapActions](https://blog.csdn.net/weixin_41364246/article/details/117572374)
> - [使用Vue3的CompositionAPI来优化代码量](https://juejin.cn/post/6917592199140458504)