<template>
  <Home>
    <el-card v-loading="loading">
      <div slot="header" class="clearfix">
        <span>{{newBlog.title}}</span>
      </div>
      <div class="datetime">
        发布 {{newBlog.publishtime}}<br>
        更新 {{newBlog.updatetime}}
      </div>
      <div v-html="mdToHtml(newBlog.content)">
      </div>
    </el-card>
  </Home>
</template>
<!--
<page-query>
query ($page: Int) {
  posts: allStrapiPost(perPage: 2, page: $page) @paginate {
    pageInfo {
      totalPages
      currentPage
    }
    edges{
      node{
        id
        title
        content
        tags{
          id
          title
        }
        author{
          id
          title
        }
        created_at
      }
    }
  }
  general:allStrapiGeneral{
    edges{
      node{
        id
        title
        subtitle
        cover{
          url
        }
      }
    }
  }
}
</page-query>
-->

<script>
// import { Pager } from 'gridsome'
import axios from 'axios'
import utils from '../utils'
import MarkdownIt from 'markdown-it'
const md = new MarkdownIt()

export default {
  name: 'HomePage',
  metaInfo: {
    title: 'new',
  },
  data(){
    return {
      newBlog:{},
      loading: true,
    }
  },
  methods: {
    mdToHtml (markdown) {
      return md.render(''+markdown);
    }
  },
  computed:{
    // general(){
    //   return this.$page.general.edges[0].node
    // }
  },
  created(){
    var that = this;
    axios.get(this.GRIDSOME_API_URL+'/blogs',
      {params: {
        _sort:'publishtime:DESC',
        _start:0,
        _limit:1
      }}
    )
    .then(function(rl){
      that.loading=false;
      
      if(rl.status == 200){
        var data = rl.data[0];
        data.updatetime=utils.formatTimeSpan(new Date(data.updatetime),'YYYY-MM-DD hh:mm:ss');
        data.publishtime=utils.formatTimeSpan(new Date(data.publishtime),'YYYY-MM-DD hh:mm:ss');
        that.newBlog=data
      }
    })
    .catch(function(){
      that.loading=false;
    })
  },
  mounted(){
  }
}
</script>

<style>
.datetime{
  font-size: 0.9rem;
  line-height: 1.5;
  color: rgb(96, 108, 113);
}
</style>
