<template>
    <div class="home pv4">
        <h1 class="tc w-100">Книги</h1>

        <div class="pa3 br3 bg-near-white w-100">
            <div class="flex">
                <div class="flex flex-column items-start w-25">
                    <span class="f6">Ключевые слова</span>
                    <el-input class="mt2" v-model="filters.search"></el-input>
                </div>
                <div class="flex flex-column items-start w-25 ml4">
                    <span class="f6">Сортировать по</span>
                    <el-select v-model="filters.field" placeholder="Select" class="mt2 w-100" clearable>
                        <el-option
                                v-for="(item, index) in ['title', 'author', 'description', 'date']"
                                :key="index"
                                :label="item"
                                :value="item">
                        </el-option>
                    </el-select>
                </div>
                <div class="flex flex-column items-start w-25 ml4">
                    <span class="f6">Направление сортировки</span>
                    <el-switch
                            class="mt2 pa3 br2 ba b--black-10 bg-white"
                            v-model="filters.sort"
                            active-color="#13ce66"
                            inactive-color="#ff4949"
                            active-value="asc"
                            inactive-value="desc"
                            active-text="Возрастание"
                            inactive-text="Убывание">
                    </el-switch>
                </div>
            </div>
            <div class="flex">
                <el-button
                        class="mt3"
                        type="primary"
                        @click="handleSearch"
                        icon="el-icon-search">Поиск</el-button>
                <el-button
                        class="mt3"
                        type="warning"
                        plain
                        @click="handleClear"
                        icon="el-icon-close">Очистить</el-button>
            </div>
        </div>

        <el-button
                type="primary"
                plain icon="el-icon-plus"
                class="mt4 w-auto"
                @click="handleDialogOpen">Добавить книгу</el-button>

        <el-table
                class="w-100 mt4"
                :data.sync="list">
            <el-table-column
                    align="left"
                    prop="title"
                    label="Заголовок">
            </el-table-column>
            <el-table-column
                    align="left"
                    prop="author"
                    label="Автор">
            </el-table-column>
            <el-table-column
                    align="left"
                    prop="description"
                    label="Описание">
            </el-table-column>
            <el-table-column
                    align="left"
                    prop="date"
                    label="Дата публикации">
                <template slot-scope="scope">
                    {{ formatDate(scope.row) }}
                </template>
            </el-table-column>
            <el-table-column
                    align="left"
                    prop="date"
                    label="Действия">
                <template slot-scope="scope">
                    <el-button
                            @click="handleDialogOpen(scope.$index, scope.row)">Редактировать</el-button>
                    <el-button
                            type="danger"
                            @click="handleDelete(scope.$index, scope.row)">Удалить</el-button>
                </template>
            </el-table-column>
        </el-table>

        <div class="mt4 tl">
            <el-pagination
                    @size-change="handleSizeChange"
                    @current-change="handleSearch"
                    :current-page.sync="filters.page"
                    :page-size="filters.count"
                    :page-sizes="[10, 100, 200, 500, 1000]"
                    layout="total, sizes, prev, pager, next"
                    :total="total">
            </el-pagination>
        </div>

        <el-dialog
                title="Новая книга"
                width="30%"
                :visible.sync="dialogVisible"
                @close="handleDialogClose">
            <BookForm ref="book" :book="book"/>
            <span slot="footer" class="dialog-footer">
        <el-button @click="handleDialogClose">Назад</el-button>
        <el-button type="primary" @click="handleDialogSubmit">Принять</el-button>
      </span>
        </el-dialog>
    </div>
</template>

<script>
    import moment from 'moment'
    import { mapGetters, mapActions } from 'vuex'
    import BookForm from '@/components/BookForm.vue'
    export default {
        name: 'home',
        components: {
            BookForm,
        },
        data() {
            return {
                dialogVisible: false,
                book: {},
                filters: {
                    search: '',
                    count: 10,
                    page: 1,
                    sort: 'desc',
                    field: ''
                }
            }
        },
        computed: {
            ...mapGetters([
                'list',
                'total',
            ]),
        },
        methods: {
            ...mapActions([
                'getList',
                'destroy',
                'filter',
                'create',
                'update'
            ]),
            formatDate({ date }) {
                return moment(date).format('DD.MM.YYYY')
            },
            handleCreate() {
                this.book = {}
            },
            handleEdit(index, v) {
                this.book = Object.assign(v)
            },
            handleDelete(index, v) {
                this.destroy(v.id)
                    .then(() => this.getList())
            },
            handleSizeChange(v) {
                this.$set(this.filters, 'count', v)
                this.handleSearch()
            },
            handleSearch() {
                const params = Object.keys(this.filters).reduce((obj, k) => {
                    if (this.filters[k]) obj[k] = this.filters[k]
                    return obj
                }, {})
                this.filter(params)
            },
            handleClear() {
                this.filters = {
                    search: '',
                    count: 10,
                    page: 1,
                    sort: 'desc',
                    field: ''
                }
                this.getList()
            },
            handleDialogOpen(index, item) {
                if (item) {
                    this.book = Object.assign({}, item)
                }
                this.dialogVisible = true
            },
            handleDialogClose() {
                this.book = {}
                this.dialogVisible = false
            },
            handleDialogSubmit() {
                this.dialogVisible = false
                if ('id' in this.book) {
                    this.update(this.book)
                        .then(() => this.getList())
                        .then(() => { this.book = {} })
                } else {
                    this.create(this.book)
                        .then(() => this.getList())
                        .then(() => { this.book = {} })
                }
            }
        },
        created() {
            this.getList()
        }
    }
</script>

<style lang="scss" scoped>
    .home {
        max-width: 1200px;
        margin: 0 auto;
        display: flex;
        flex-direction: column;
        align-items: start;
        h1 {
            margin-bottom: 3rem;
        }
    }
</style>
