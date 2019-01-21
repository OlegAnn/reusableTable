<template>
  <section class="hello">
    <reusable-table
      :titles='items[0].titles'
      :items='items[0].items'
    >
      <template 
        class="wrapper_body"
        slot-scope="props"
      >
        <li class="column">
          <input 
            type="text"
            v-model="props.item.name"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.height"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.mass"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.hair_color"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.skin_color"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.eye_color"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.birth_year"
          >
        </li>
        <li class="column">
          <input 
            type="text" 
            v-model="props.item.gender"
          >
        </li>
      </template>
    </reusable-table>
    <p style="text-align: center; color: red">another component with another data </p>
    <reusable-table
      :titles='anotherItems[0].titles'
      :items='anotherItems[0].items'
    >
      <template 
        class="wrapper_body"
        slot-scope="props"
       >
          <li class="column">
            <input 
              type="text" 
              v-model="props.item.company"
            >
          </li>
          <li class="column">
            <input 
              type="text" 
              v-model="props.item.phone_model"
            >
          </li>
          <li class="column">
            <input 
              type="text" 
              v-model="props.item.color"
            >
          </li>
          <li class="column">
            <input 
              type="text" 
              v-model="props.item.production_year"
            >
          </li>
      </template>
    </reusable-table>
  </section>
</template>

<script>
import ReusableTable from '@/components/ReusableTable'
import { sw } from '@/data/starWars.js';
import { anotherData } from '@/data/anotherData.js';
export default {
  name: 'Home',
    components: {
      ReusableTable
  },
  data: () => ({
    items: sw,
    anotherItems: anotherData
  })
}
</script>
<style scoped>
  h3 {
    margin: 40px 0 0;
  }
  li {
    display: inline-block;
    margin: 0 10px;
  }
  a {
    color: #42b983;
  }
</style>