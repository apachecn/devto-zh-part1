# TIL - Vue.js 事件总线更清晰

> 原文：<https://dev.to/terrierscript/til---vuejs-event-bus-more-clearly-1a95>

Vue.js 事件总线技术有时很有用。

```
// ModalEvent.js
import Vue from "vue"

export const ModalEvent = new Vue() 
```

```
<script>
import Vue from "vue"
import VModal from "vue-js-modal"
import { ModalEvent } from "./ModalEvent"

Vue.use(VModal)

export default {
  created(){
    ModalEvent.$on("openModal", () => {
      this.$modal.show("hello-modal")
    })
  }
}
</script> 
```

```
<!-- event trigger component -->
<script>
import { ModalEvent } from "./ModalEvent"

export default {
  methods:{
    onClick(){
      ModalEvent.$emit("openModal")
    }
  }
}
</script> 
```

但是有个小问题是`Event.$on`和`Event.$emit`的事件签名是字符串，很容易出错。

# 解

我发现它可以用`Event.methods`来解决。

```
// ModalEvent.js
import Vue from "vue"

const OPEN_MODAL = "openModal"
export const ModalEvent = new Vue({
  methods: {
    emitOpenModal(event){
      this.$emit(OPEN_MODAL, event)
    },
    onOpenModal(cb){
      this.$on(OPEN_MODAL, cb)
    }
  }
}) 
```

```
<script>
import Vue from "vue"
import VModal from "vue-js-modal"
import { ModalEvent } from "./ModalEvent"

Vue.use(VModal)

export default {
  created(){
    ModalEvent.onOpenModal( () => {
      this.$modal.show("hello-modal")
    })
  }
}
</script> 
```

```
<script>
import { ModalEvent } from "./ModalEvent"

export default {
  methods:{
    onClick(){
      ModalEvent.emitOpenModal()
    }
  }
}
</script> 
```