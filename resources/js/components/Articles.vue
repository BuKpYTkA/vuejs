<template>
    <div>
        <h2>Articles</h2>
        <ul class="pagination">
            <li v-bind:class="[{disabled: !pagination.prevPageUrl}]" class="page-item">
                <a class="page-link" href="#" @click="fetchArticles(pagination.prevPageUrl)">Previous</a></li>
            <li class="page-item disabled"><a class="page-link text-dark" href="#">Page {{pagination.currentPage}} of {{pagination.lastPage}}</a></li>
            <li v-bind:class="[{disabled: !pagination.nextPageUrl}]" class="page-item">
                <a class="page-link" href="#" @click="fetchArticles(pagination.nextPageUrl)">Next</a></li>
        </ul>
        <div class="card card-body mb-2" v-for="article in articles"
             v-bind:key="article.id">
            <h3>{{ article.title }}</h3>
            <p>{{ article.text }}</p>
            <hr>
            <button class="btn btn-danger" @click="deleteArticle(article.id)">Delete Article</button>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                articles: [],
                article: {
                    id: '',
                    title: '',
                    text: '',
                    userId: ''
                },
                pagination: {},
                edit: false
            }
        },

        created() {
            this.fetchArticles();
        },

        methods: {
            fetchArticles(pageUrl) {
                pageUrl = pageUrl || 'api/articles';
                let vm = this;
                fetch(pageUrl)
                    .then(res => res.json())
                    .then(res => {
                        this.articles = res.data;
                        vm.makePagination(res.meta, res.links);
                    }).catch(err => console.log(err));
            },
            makePagination(meta, links) {
                let pagination = {
                    currentPage: meta.current_page,
                    lastPage: meta.last_page,
                    nextPageUrl: links.next,
                    prevPageUrl: links.prev
                };
                this.pagination = pagination;
            },
            deleteArticle(id) {
                if (confirm('Are you sure?')) {
                    fetch(`api/article/${id}`, {
                        method: 'delete'
                    })
                        .then(res => res.json())
                        .then(data => {
                            alert('Article deleted');
                            this.fetchArticles();
                        })
                        .catch(err => console.log(err));
                }
            }
        }
    }
</script>

<style scoped>

</style>