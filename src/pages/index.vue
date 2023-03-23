<script lang="ts" setup>
import { Input, Tag } from 'ant-design-vue'
import { EditOutlined } from '@ant-design/icons-vue'
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

const onDelete = (i: number) => {
  arr.value.splice(i, 1)
}

const edit = ref(false)
const toggleEdit = () => {
  edit.value = !edit.value
}
</script>

<template>
  <div>
    <template v-if="!edit">
      <div class="w-1/2 flex items-center gap-4">
        <Input type="text" readonly :value="totalExpression" />
        <EditOutlined class="cursor-pointer" @click="toggleEdit" />
      </div>
    </template>
    <template v-else>
      <div class="w-1/2">
        <div class="flex items-center mb-3">
          <Tag color="green" class="cursor-pointer">
            and
          </Tag>
          <Tag color="green" class="cursor-pointer">
            or
          </Tag>
          <Tag color="geekblue" class="cursor-pointer">
            (
          </Tag>
          <Tag color="geekblue" class="cursor-pointer">
            )
          </Tag>
        </div>
        <div class="w-full min-h-[50px] border-1 border-gray-400 rounded-lg flex flex-wrap gap-y-2 p-3">
          <div v-for="(item, index) in arr" :key="item.id" class="cursor-pointer" :data-id="item.name">
            <Tag
              v-if="['and', 'or', '(', ')'].includes(item.name)"
              :color="['and', 'or'].includes(item.name) ? 'green' : 'geekblue'"
              closable
              @close="onDelete(index)"
            >
              {{ item.name }}
            </Tag>
            <Tag v-else>
              {{ item.name }}
            </Tag>
          </div>
        </div>
      </div>
      <div class="flex items-center gap-2 mt-3">
        <span>当前表达式：</span>
        <p>{{ totalExpression }}</p>
      </div>
    </template>
  </div>
</template>

<style>
.ant-tag .ant-tag-close-icon {
  transform: translateY(-2px);
}
</style>
