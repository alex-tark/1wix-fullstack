<template>
    <div class="pa4 ba b--black-10">
        <div class="mt4 flex flex-column">
            <span class="tl">Автор</span>
            <el-input
                    class="mt2"
                    placeholder="Имя автора"
                    v-model="item.author"></el-input>
        </div>
        <div class="mt4 flex flex-column">
            <span class="tl">Название</span>
            <el-input
                    class="mt2"
                    placeholder="Полное название книги"
                    v-model="item.title"></el-input>
        </div>
        <div class="mt4 flex flex-column">
            <span class="tl">Описание</span>
            <el-input
                    class="mt2"
                    placeholder="Краткое описание"
                    v-model="item.description"></el-input>
        </div>
        <div class="mt4 flex flex-column">
            <span class="tl">Дата публикации</span>
            <el-date-picker
                    class="mt2"
                    v-model="item.date"
                    type="date"
                    placeholder="Дата публикации"
                    format="dd.MM.yyyy"
                    value-format="yyyy-MM-dd">
            </el-date-picker>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            book: {
                type: Object,
                required: true
            }
        },
        computed: {
            item() {
                return this.book
            }
        }
    }
</script>

<style>
</style>
