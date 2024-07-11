<script lang="ts" setup>
    import { ref, computed, onMounted } from "vue";
    import type { Ref } from 'vue'

    import ListItem from './ListItem.vue'
    type Item = {
        title: string,
        checked?: boolean
    }

    const storageItems: Ref<Item[]> = ref([]) //local storage array of Items

    // const listItems: Ref<Item[]> =  ref([
    //     {title: 'Make a todo list app', checked: true},
    //     {title: 'Make peace in the middle east', checked: false},
    //     {title: 'Play the flute', checked: false},
    //     {title: 'List all expenses', checked: false},
    //     {title: 'Master this Vue', checked: false},
    //     {title: 'Publish this list'},
    // ])

    const setToStorage = (items: Item[]) : void => {
        localStorage.setItem('list-items', JSON.stringify(items))
    }

    const getFromStorage = (): Item[] | [] => {
        const stored = localStorage.getItem('list-items')
        return (stored) ? JSON.parse(stored) : []
    }

    const initListItems = (): void => {
        console.log('storageItems.value.length ',storageItems.value.length);
        
        //! Check this condition... ?. optional chaining
        //meaning if storageItems has value check the length
        if (storageItems.value?.length === 0) {
            const listItems = [
                {title: 'Make a todo list app', checked: true},
                {title: 'Make peace in the middle east', checked: false},
                {title: 'Play the flute', checked: false},
                {title: 'List all expenses', checked: false},
                {title: 'Master this Vue', checked: false},
                {title: 'Publish this list'}
            ]
            setToStorage(listItems)
            storageItems.value = listItems
        }
    }

    onMounted(() => {
        initListItems()
        storageItems.value = getFromStorage()
    })

    const updateItem = (item: Item): void => {
        const updatedItem = findItemInList(item)
        //* bcoz findItemInList returns Item or undefined
        if (updatedItem) {
            toggleItemChecked(updatedItem)
            setToStorage(storageItems.value)
        }
    }

    const findItemInList = (item: Item): Item | undefined => {
        return storageItems.value.find(
            (itemInList: Item) => itemInList.title === item.title
        )
    }

    const toggleItemChecked = (item: Item): void => {
        item.checked = !item.checked
    }

    const sortedList = computed(() => [...storageItems.value].sort((a,b) => (a.checked ? 1:0) - (b.checked ? 1:0)))
</script>

<template>
    <ul>
        <li :key="key" v-for="(item, key) in sortedList">
            <ListItem :is-checked="item.checked" @click.prevent="updateItem(item)">{{ item.title }}</ListItem>
        </li>
    </ul>
</template>

<style>
ul {
    list-style: none;
}
li {
    margin: 0.4rem 0;
}
</style>