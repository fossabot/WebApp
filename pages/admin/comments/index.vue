<template>
    <section class="">
        <h1 class="is-size-3">{{ $t('component.admin.manageComments', 'Manage Comments') }}</h1>
        <br>
        <div class="field">
            <p class="control"><input type="text" placeholder="search Comments" v-model="commentSearch" class="input"></p>
        </div>
        <no-ssr>
            <v2-table
                  :data="comments.data"
                  :loading="commentsLoading"
                  :total="comments.total"
                  :shown-pagination="true"
                  :pagination-info="paginationInfo"
                  @page-change="handlePageChange">
                <v2-table-column label="Username" width="50" align="left">
                    <template slot-scope="row">
                        <div @click="openProfile(row.user)" style="white-space: nowrap;" :class="{'link': !!row.user.slug}" class="cell-name">
                            <hc-avatar :user="row.user" style="display: inline-block; float: left;" />&nbsp;<div style="display: inline-block; padding: 5px 10px;">{{ row.user.name }}</div>
                        </div>
                    </template>
                </v2-table-column>
                <v2-table-column label="Content" prop="contentExcerpt" align="left">
                </v2-table-column>
                <v2-table-column label="Actions" width="15">
                    <template slot-scope="row">
                        <hc-icon class="action" set="fa" icon="pencil"></hc-icon>
                    </template>
                </v2-table-column>
            </v2-table>
        </no-ssr>
    </section>
</template>

<script>
  import feathers from '~/plugins/feathers'
  import moment from 'moment'

  import 'beautify-scrollbar/dist/index.css'
  import 'v2-table/dist/index.css'

  let commentLimit = 10

  export default {
    middleware: 'admin',
    layout: 'admin',
    data () {
      return {
        commentsLimit: commentLimit,
        isLoading: false,
        commentsLoading: true,
        results: '',
        comments: [],
        commentSearch: '',
        currentPage: 1,
        paginationInfo: {
          text: this.paginationText
        }
      }
    },
    async asyncData () {
      const limit = commentLimit
      const comments = await feathers.service('comments').find({
        query: {
          $limit: limit
        }
      })
      return {
        comments: comments,
        commentsLoading: false,
        commentsLimit: limit
      }
    },
    filters: {
      formatDate (val) {
        return moment(val).format('d.m.Y')
      }
    },
    computed: {
      totalPages () {
        return this.comments.total / this.commentsLimit
      },
      paginationText () {
        return `<span>Total of <strong>${this.comment ? this.comment.total : 0}</strong>, <strong>${this.commentsLimit}</strong> per page</span>`
      }
    },
    watch: {
      commentSearch (val) {
        this.searchComments()
      }
    },
    methods: {
      openProfile (user) {
        if (user.slug) {
          this.$router.push(`/profile/${user.slug}`)
        }
      },
      async handlePageChange (page) {
        this.currentPage = page
        this.commentsLoading = true
        const start = (page - 1) * this.commentsLimit + 1

        this.comments = await feathers.service('comments').find({
          query: {
            $limit: this.commentsLimit,
            $skip: start
          }
        })
        this.commentsLoading = false
      },
      async searchComments () {
        this.commentsLoading = true
        this.comments = await feathers.service('comments').find({
          query: {
            content: {$search: this.commentSearch}
          }
        })
        this.commentsLoading = false
      }
    },
    head () {
      return {
        title: 'Kommentare verwalten'
      }
    }
  }
</script>

<style scoped lang="scss">
    @import 'assets/styles/utilities';

    .cell-name {
        font-weight: bold;
    }

    .link {
        white-space: nowrap;
        cursor: pointer;
        color: $primary;
    }

    .fa-check-circle {
        color: $primary;
    }

    .action {
        cursor: pointer;
    }
</style>
