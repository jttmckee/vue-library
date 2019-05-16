<template>
  <div id="app">
    <table>
      <tr>
        <th v-for="(_, heading) in headings">{{ heading }}</th>
        <th>Delete?</th>
      </tr>
      <tr v-for="(item, itemIndex) in items">
        <td v-for="(part, index) in item">
          <input
            type="checkbox"
            v-if="typeof part === 'boolean'"
            v-model="items[itemIndex][index]"
          />
          <span v-else>{{ part }}</span>
        </td>
        <td>
          <button class="delete-button" @click="deleteItem(itemIndex)">
            🗑
          </button>
        </td>
      </tr>
      <tr>
        <td v-for="(type, heading, index) in headings">
          <input
            :type="type"
            :placeholder="type === 'number' ? 0 : heading"
            v-model="newItem[index]"
            :class="errorClasses[index]"
          />
        </td>
        <td>
          <button class="submit-button" type="submit" @click="createNew">
            New
          </button>
        </td>
      </tr>
    </table>
    <div class="error-messages" v-if="errorMessages.length > 0">
      <h2>Errors</h2>
      <ul>
        <li v-for="message in errorMessages">{{message}}</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  methods: {
    _validateItem(item, minLength, name,index) {
      if (!item || item.length < 2) {
        this.errorMessages.push(`${name} must be at least ${minLength} characters`)
        this.errorClasses[index] = 'error-input'
        console.log(name)
        return false
      } else {
        return true
      }
    },
    _validateItems() {
      this.errorMessages = []
      this.errorClasses = ['', '', '', '']
      let valid =  this._validateItem(this.newItem[0], 3, 'Title',0)
       valid =  this._validateItem(this.newItem[1], 2, 'Author',1)  && valid

      if (
        !this.newItem[2] ||
        isNaN(this.newItem[2]) ||
        !(Number.isInteger(Number(this.newItem[2]))) ||
        this.newItem[2] < 1
      ) {
        valid = false
        this.errorMessages.push(
          'Number of pages must be a whole number greater than 0'
        )
        this.errorClasses[2] = 'error-input'
      }
      console.log(valid)
      return valid
    },
    deleteItem(index) {
      this.items.splice(index, 1)
    },
    createNew() {
      if (this._validateItems()) {
        this.items.push(this.newItem)
        console.log(this.newItem)
        this.newItem = [, , , false]
      }
    }
  },
  mounted() {
    if (localStorage.items) {
      this.items = JSON.parse(localStorage.items)
    }
  },
  watch: {
    items(newItems) {
      console.log(newItems)
      localStorage.items = JSON.stringify(this.items)
    }
  },
  data() {
    return {
      headings: {
        Title: 'text',
        Author: 'text',
        'Number of Pages': 'number',
        'Read?': 'checkbox'
      },
      items: [
        ['The Lord of The Rings', 'J R R Tolkien', 1250, false],
        ['An Example', 'A. N. Other', 57, false],
        ['Testing', 'Tester', 3, true]
      ],
      newItem: [, , , false],
      errorClasses: ['', '', '', ''],
      errorMessages: []
    }
  }
}
</script>

<style>
table {
  border: 2px solid #840;
  border-collapse: collapse;
}
th {
}
td,
th {
  padding: 2px 6px;
  border: 1px solid Gray;
  font-size: 1.1rem;
  font-family: Arial, cursive;
}
th {
  background-color: #905007;
  color: white;
  width: auto;
}
tr {
  background-color: white;
}
tr:nth-child(2n) {
  background-color: AntiqueWhite;
}
td input {
  border: none;
  height: 1.2rem;
  font-size: 1.1rem;
  width: 100%;
  background-color: #fffff0;
}
td input[type='number'] {
  width: 6rem;
  border: 1px solid lightGray;
  border-radius: 10px;
  background-color: #fffffb;
}
td:last-child {
  text-align: center;
}
.delete-button {
  background-color: transparent;
  border: none;
  transition: all 200ms ease-out;
  font-size: 1rem;
}
.delete-button:active {
  font-size: 1.2rem;
}
.submit-button {
  font-size: 1.1rem;
  background-color: #ffffe0;
  border-radius: 10px;
  color: #320;
}
tr:last-child td {
  border-top: 3px dashed lightGray;
  padding: 6px 6px;
  background-color: #fffff0;
}
.error-input {
  border: 1px solid red !important;
  border-radius: 3px;
}
.error-messages {
  color: red;
}
.error-messages li {
  font-size: 1.1rem;
}
</style>
