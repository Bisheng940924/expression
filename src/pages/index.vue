<script lang="ts" setup>
import { Input, Tag } from 'ant-design-vue'
import { EditOutlined } from '@ant-design/icons-vue'
import Sortable from 'sortablejs'
import 'ant-design-vue/dist/antd.css'
interface Data {
  conditions: {
    hwId: string
    name: string
  }[]
  expression: string
}

// 生成随机id
const genID = () => {
  return Math.random().toString(36).substr(2)
}

const data = ref<Data>({
  conditions: [
    {
      hwId: 'b789c8b6-c920-11ed-932c-0255ac1202d7',
      name: '条件一',
    },
    {
      hwId: 'b78a16d8-c920-11ed-932c-0255ac1202d7',
      name: '条件二',
    },
    {
      hwId: 'b78a3dea-c920-11ed-932c-0255ac1202d7',
      name: '条件三',
    },
    {
      hwId: 'b78a3dec-c920-11ed-932c-0255ac1202d7',
      name: '条件四',
    },
    {
      hwId: 'b78a64fe-c920-11ed-932c-0255ac1202d7',
      name: '条件五',
    },
    {
      hwId: 'b78a8c10-c920-11ed-932c-0255ac1202d7',
      name: '条件六',
    },
    {
      hwId: 'b78ab322-c920-11ed-932c-0255ac1202d7',
      name: '条件七',
    },
    {
      hwId: 'b78ada34-c920-11ed-932c-0255ac1202d7',
      name: '条件八',
    },
    {
      hwId: 'b78b0146-c920-11ed-932c-0255ac1202d7',
      name: '条件九',
    },
  ],
  expression: 'b789c8b6-c920-11ed-932c-0255ac1202d7 and b78a16d8-c920-11ed-932c-0255ac1202d7 and b78a3dea-c920-11ed-932c-0255ac1202d7 and b78a3dec-c920-11ed-932c-0255ac1202d7 and b78a64fe-c920-11ed-932c-0255ac1202d7 and (b78a8c10-c920-11ed-932c-0255ac1202d7 or b78ab322-c920-11ed-932c-0255ac1202d7) and (b78ada34-c920-11ed-932c-0255ac1202d7 or b78b0146-c920-11ed-932c-0255ac1202d7)',
})

const convertData = () => {
  const str = data.value.expression.replace(/\s+/g, '')
  return str.split(/(and|or|\)|\()/).filter(Boolean).map((item) => {
    const target = data.value.conditions.find(condition => item.includes(condition.hwId))
    if (target) {
      return {
        ...target,
        id: target.hwId,
      }
    }
    else {
      return {
        name: item,
        id: genID(),
      }
    }
  })
}

const arr = ref(convertData())
const totalExpression = computed(() => {
  return arr.value.map(i => i.name).join(' ')
})
const originExpression = computed(() => {
  return arr.value.map((item: any) => {
    if (item.hwId)
      return item.hwId

    else
      return item.name
  }).join(' ')
})

const onDelete = (i: number) => {
  arr.value.splice(i, 1)
}

const edit = ref(false)
const toggleEdit = () => {
  edit.value = !edit.value
}

const list = ref([
  {
    name: 'and',
    id: Symbol('and'),
  },
  {
    name: 'or',
    id: Symbol('or'),
  },
  {
    name: '(',
    id: Symbol('('),
  },
  {
    name: ')',
    id: Symbol(')'),
  },
])
const onClone = (item: any, index: number) => {
  console.log(item)

  const clone = {
    ...item,
    id: genID(),
  }
  arr.value.splice(index, 0, clone)
}
onMounted(() => {
  const expressEl = document.getElementById('expression') as HTMLElement
  const listEl = document.getElementById('list') as HTMLElement

  const listSortable = new Sortable(listEl, {
    group: {
      name: 'expression',
      pull: 'clone',
      put: false,
    },
    animation: 300,
    onEnd: (evt) => {
      const { newIndex, oldIndex, item } = evt
      if (newIndex != null && oldIndex != null) {
        const target = list.value[oldIndex]
        arr.value.splice(newIndex, 0, target)
      }
      // 删除item
      expressEl.removeChild(item)
    },
  })
  const expSortable = new Sortable(expressEl, {
    group: 'expression',
    sort: true,
    ghostClass: 'ghost',
    animation: 300,
    onEnd: (evt) => {
      const { newIndex, oldIndex } = evt
      if (newIndex != null && oldIndex != null && (newIndex !== oldIndex)) {
        // 交换位置
        const temp = arr.value[oldIndex]
        arr.value.splice(oldIndex, 1)
        arr.value.splice(newIndex, 0, temp)
      }
    },
  })
})
</script>

<template>
  <div>
    <div v-show="!edit" class="w-1/2 flex items-center gap-4">
      <Input type="text" readonly :value="totalExpression" />
      <EditOutlined class="cursor-pointer" @click="toggleEdit" />
    </div>
    <div v-show="edit">
      <div class="w-1/2">
        <div id="list" class="flex items-center mb-3">
          <div v-for="element in list" :key="element.id">
            <Tag
              :color="['and', 'or'].includes(element.name) ? 'green' : 'geekblue'"
              style="cursor: pointer;"
            >
              {{ element.name }}
            </Tag>
          </div>
        </div>
        <div id="expression" class="w-full min-h-[50px] border-1 border-gray-400 rounded-lg flex flex-wrap gap-y-2 p-3">
          <div v-for="(item, index) in arr" :key="item.id" class="cursor-pointer" :data-id="item.name">
            <Tag
              v-if="['and', 'or', '(', ')'].includes(item.name)"
              :color="['and', 'or'].includes(item.name) ? 'green' : 'geekblue'"
              closable
              @close="onDelete(index)"
            >
              {{ item.name }}
            </Tag>
            <div v-else @dblclick="onClone(item, index)">
              <Tag>
                {{ item.name }}
              </Tag>
            </div>
          </div>
        </div>
      </div>

      <div class="flex items-center gap-2 mt-3">
        <span>当前表达式：</span>
        <p>{{ totalExpression }}</p>
      </div>
      <div class="flex items-start gap-2 mt-3">
        <span class="flex-shrink-0">原始表达式：</span>
        <p class="w-1/2 text-left">
          {{ originExpression }}
        </p>
      </div>
    </div>
  </div>
</template>

<style>
.ant-tag .ant-tag-close-icon {
  transform: translateY(-2px);
}
.flip-list-move {
  transition: transform 0.5s;
}
.no-move {
  transition: transform 0s;
}
.ghost {
  opacity: 0.5;
  background: #c8ebfb;
}
.list-group {
  min-height: 20px;
}
.list-group-item {
  cursor: move;
}
.list-group-item i {
  cursor: pointer;
}
</style>
