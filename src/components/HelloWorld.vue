<template>
  <div>
    <p>{{ vTitle }}</p>

    <div class="my-3 text-primary">
      <p>{{ append }}</p>
    </div>

    <div class="form-group d-flex">
      <input v-model="todo" type="text" class="form-control" />
      <button class="btn btn-primary" @click="add">Add</button>
    </div>
    <p>[TodoLength: {{ todoLength }}, Items quantity: {{ itemsQuantity }}]</p>

    <div class="list-group">
      <div
        class="list-group-item d-flex justify-content-between"
        v-for="(item,index) in items"
        :key="index"
      >
        <span>{{ item }}</span>
        <button class="close" @click="remove(index)">
          <span>&times;</span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch, onBeforeMount, onMounted } from '@vue/composition-api';

// функция которая не возвращает ничего в шаблон
function useCount() {
  const count = ref(1);
  const double = computed({
    get: () => count.value * 2,
    set: val => { count.value = val - 1 },
  });

  console.log(count.value);
  console.log(double.value);
}

// функция использующая на входе props
function useTitle(props) {
  const vTitle = computed(() => `-${props.title}-`);

  return {
    vTitle
  };
}

// функция использующая на входе ref заданный в setup()
function useTodo(todo) {
  const todoLength = ref(0);

  watch(
    () => todo.value.length,
    length => {
      todoLength.value = length;
    },
    {
      lazy: false,
    }
  )

  return {
    todoLength
  };
}

// функция использующая на входе ref заданный в setup()
function useItems(todo) {
  const items = ref(['This', 'is']);
  const itemsQuantity = computed(() => items.value.length);
  const append = ref('');

  watch(
    // getter
    () => items.value,
    // callback
    items => {
      append.value = '';
      items.forEach( item => {
        append.value += item + ' ';
      });
    },
    // watch options
    {
      lazy: false,
    },
  )

  const add = () => {
    if (todo.value) {
      items.value.push(todo.value);
      todo.value = '';
    }
  };

  const remove = index => {
    items.value.splice(index, 1);
  };

  return {
    items,
    itemsQuantity,
    append,
    add,
    remove,
  };
}

export default {
  props: {
    title: String,
    initInput: String,
  },

  setup(props) {
    const todo = ref(props.initInput);

    onBeforeMount(() => {
      console.log('V3 beforeMount!');
    })

    onMounted(() => {
      console.log('V3 mounted!');
    })

    return {
      todo,
      ...useCount(),
      ...useTitle(props),
      ...useItems(todo),
      ...useTodo(todo),
    };
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
