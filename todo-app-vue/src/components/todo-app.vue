<template>
  <div class="todo-app">
    <div class="container">
      <div class="row">
        <div class="col-12">
          <form @submit.prevent="addNewItem($event)">
            <input name="item" v-model="item" type="text" placeholder="Enter an activity" autocomplete="off">
            <button type="submit" class="btn-icon button flaticon-add pointer"></button>
          </form>
          <div class="row">
            <div class="col-12">
              <div class="fliter-list d-flex flex-row">
                <p class="mr-auto align-self-center pointer w-100 text-left">
                  fliter list
                </p>
                <button :class="{'active':filterBy == 'all'}" class="action-filter all btn-icon align-self-center pointer" @click="setFilter('all')">ALL</button>
                <button :class="{'active':filterBy == 'doing'}" class="action-filter doing btn-icon align-self-center pointer flaticon-verified" @click="setFilter('doing')"></button>
                <button :class="{'active':filterBy == 'done'}" class="action-filter done btn-icon align-self-center pointer flaticon-success" @click="setFilter('done')"></button>
              </div>
              <ul class="todos d-flex flex-column">
                <li v-if="items.length" v-for="(plate,i) in items" :key="i" :class="['flex-row'  , {'event-all':filterBy ==  'all','event-done': filterBy == 'done'  && plate.done
        ,'event-doing':filterBy == 'doing' && !plate.done }]">
                  <input @keyup.esc="cancelEdit(plate,i)" v-todo-focus="plate == editedTodo" @keyup.enter="setEdit(plate,i)" type="text" :id="i" v-model="plate.text" :class="['mr-auto inputItem align-self-center w-100 ' ,{'d-none':!plate.isEdit}]" ref="focusActive[i]" />
                  <p :class="['text-left itemText mr-auto align-self-center pointer w-100' , {'d-none':plate.isEdit}]" @click="editeTitle(plate,i)">
                    {{plate.text}}
                  </p>
                  <button class="removeItem btn-icon align-self-center pointer flaticon-delete-button" @click="removeEvent(plate,i)"></button>
                  <button :class="['checkItem btn-icon align-self-center pointer ' , {'flaticon-verified':!plate.done , 'flaticon-success':plate.done}]" @click="toggleDone(plate,i)"></button>
                </li>
              </ul>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'todo-app',
  data () {
    return {
      items: [],
      filterBy: 'all',
      item: '',
      editedTodo: null,
      itemObject: {
        isEdit: false,
        done: false,
        text: ''
      }
    }
  },
  directives: {
    'todo-focus': function (el, binding) {
      if (binding.value) {
        el.focus()
      }
    }
  },
  mounted () {
    this.items = this.mangeStroge('get') || []
  },
  methods: {
    mangeStroge (query) {
      if (query == 'set') {
        localStorage.setItem('items', JSON.stringify(this.items))
        return
      }
      if (query == 'get') {
        return JSON.parse(localStorage.getItem("items"))
      }
    },
    lengthString (value) {
      if (!value.replace(/\s/g, "").length) {
        alert("input is in empty");
      }
      return value.replace(/\s/g, "").length;
    },
    setFilter (filterBy) {
      this.filterBy = filterBy
    },
    addNewItem (e) {
      if (!this.lengthString(this.item)) return
      let item = { ...this.itemObject }
      item.text = this.item
      e.target.reset();
      this.item = ''
      this.items.push(item)
      this.mangeStroge('set')
    },
    removeEvent (plate, i) {
      this.$delete(this.items, i)
      this.mangeStroge('set')
    },
    toggleDone (plate, i) {
      this.$set(this.items[i], 'done', !this.items[i].done)
      this.mangeStroge('set')
    },
    editeTitle (plate, i) {
      this.$set(this.items[i], 'isEdit', !this.items[i].isEdit)
      this.editedTodo = plate
      this.mangeStroge('set')
    },
    setEdit (plate, i) {
      this.$set(this.items[i], 'isEdit', false)
      this.editedTodo = null
      this.mangeStroge('set')
    },
    cancelEdit (plate, i) {
      this.$set(this.items[i], 'text', this.mangeStroge('get')[i].text)
      this.$set(this.items[i], 'isEdit', false)
      this.editedTodo = null
      this.mangeStroge('set')
    }
  }
}
</script>

<style>
</style>
