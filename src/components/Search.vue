<template>
  <div class="container-fluid wrapper bg-white p-lg-3 p-sm-1">
      <div class="row p-lg-5 h-100" v-if="panel1 == true">
        <div class=" col-12 intro-container p-lg-5 fade-in">
          <div class="row">
            <div class="col-sm-5 ml-lg-4 mt-lg-5 mb-lg-5">
              <p class="intro-1 font-weight-bolder">Find GitHub</p>
              <p class="intro-2 font-weight-bolder">User</p>
               <input v-model="searchText1" @input="switchText" class="p-4 mt-4 mb-5 search-box border-0 col-12" type="text" placeholder="Enter Username">
            </div>

            <div class="col-sm-6 panel-img">
             
            </div>

          </div>
        </div>
      </div>

      <div class="row pl-lg-5 pr-lg-5 pt-1 m-0 h-100" v-if="panel2 == true">
        <div class=" col-12 pl-lg-3 pr-lg-3 pt-1 fade-in">
          <div class="row m-0">
            <div class="col-sm-5 pl-lg-4 pb-lg-2 ">
                <div class="form-row">
                  <div class="form-group col-lg-6 col-md-12 col-sm-12">
                    <!-- <input v-model="searchText2" type="text" ref="text2" class="search-box-2 p-3 border-0 col-12" autofocus placeholder="Email"> -->
                    <input v-model="searchText2" @keyup.enter="search" autofocus class="form-control form-control-lg shadow" type="text" placeholder="Enter a username">
                  </div>
                  <div class="form-group col-md-6 col-sm-6">
                    <select v-model="per_page_items" class="form-control form-control-lg col-lg-4 mr-2 pl-2 float-left shadow ">
                      <option>1</option>
                      <option>2</option>
                      <option>3</option>
                      <option>4</option>
                      <option>5</option>
                      <option>6</option>
                      <option>7</option>
                      <option>8</option>
                      <option>9</option>
                      <option>10</option>
                    </select>
                    <button @click="search" :disabled="searchText2 ==''"  type="button" class="btn btn-primary btn-lg col-md-5 col-sm-5 float-left"> <i class="fa fa-search"></i> </button>
                    <!-- <button type="button" @click="search" class="btn-search p-3 border-0 col-8">Search</button> -->
                  </div>
                </div>


                <div class="result-list" >

                  <div v-if=" loader === true" class="row justify-content-center m-0 mt-5">
                    <div class="db-spinner mt-5"></div>
                  </div>

                  <div data-aos="fade-in" @click="preview(user)" v-for="user in result " :key="user.id" class="card col-lg-11  list-card mb-2">
                    <div class="card-body p-2" >
                      <div class="row m-0">
                        <div class="col-2 p-0 m-0"> <img class="img-fluid rounded-circle" :src=user.avatar_url alt=""></div>
                        <div class="col-10 pl-2 pr-2 pt-0 m-0">
                          <p class="name p-0 m-0 mt-2 font-weight-bold">{{user.login}} </p>
                          <p class="role p-0 m-0 small">{{(user.html_url).slice(8)}}</p>  
                        </div>
                      </div>
                      </div>
                    </div>
                </div>
                   <nav aria-label="" class="mt-3 mb-0 " v-if="rawData.length > 0">
                          <ul class="pagination">
                            <li class="page-item   mr-2" :disabled ="current_page == 1">
                              <button @click="prev()" class="page-link" :disabled ="current_page == 1" href="#" tabindex="-1"> <i class="fa fa-arrow-left" aria-hidden="true"></i> Prev</button>
                            </li>
                          
                            <li class="page-item  ml-2" :disabled ="current_page == total_pages">
                              <button @click="next()" class="page-link " :disabled ="current_page == total_pages" href="#">Next <i class="fa fa-arrow-right" aria-hidden="true"></i></button>
                            </li>
                          </ul>
                    </nav>
            </div>

            <div class="col-sm-7 pt-0 preview" v-if="Object.keys(previewUser).length !== 0" >
              <div class="card border-0" v-if="Object.keys(previewUser).length !== 0">
                <div class="row justify-content-center pt-3">
                     <div class="col-4 p-0 m-0"> 
                       <img  class="img-fluid rounded-circle shadow" :src=previewUser.avatar_url alt="">
                       
                     </div>
                     <div class="col-7 p-0 m-0">
                       <div class="row justify-content-start pl-5 pr-5 pt-3 font-weight-bold">
                      <p class="card-title text-center font-weight-bold mb-0 h2-lg ml-2" v-if="previewUser.name !==null"> <a :href="previewUser.html_url" target="blank">{{previewUser.name}}</a> </p>
                      <p class="card-title text-center font-weight-bold mb-0 h2-lg" v-else> <a :href="previewUser.html_url" target="blank">{{previewUser.login}}</a> </p>
                        <!-- <p class="small text-center mb-0 mt-2"><i class="fa fa-at"></i> {{(previewUser.html_url)}}</p> -->
                          <div class="card-body  p-1 col-12" v-if="previewUser.company !==null">
                            <span class="badge badge-light"><p class="p-0 m-0 font-weight-bold"> <i class="fa fa-building">&nbsp; {{previewUser.company}}</i> </p> </span> 
                          </div> 
                          <div class="card-body  p-1 col-12" v-if="previewUser.location !==null">
                             <span class="badge badge-light"><p class="p-0 m-0 font-weight-bold"> <i class="fa fa-map-marker">&nbsp;  {{previewUser.location}}</i> </p> </span> 
                          </div>
                          <div class="card-body  p-1 col-12" v-if="previewUser.email !==null">
                             <span class="badge badge-light"><p class="p-0 m-0 font-weight-bold"> <i class="fa fa-at">&nbsp;  {{previewUser.email}}</i> </p> </span> 
                          </div>
                           <div class="card-body  p-1 col-12" v-if="previewUser.twitter_username !==null">
                             <span class="badge badge-light"><p class="p-0 m-0 font-weight-bold"> <i class="fa fa-twitter">&nbsp;  {{previewUser.twitter_username}}</i> </p> </span> 
                          </div>
                           <div class="card-body  p-1 col-12" v-if="previewUser.blog !==(null || '')">
                             <span class="badge badge-light"><p class="p-0 m-0 font-weight-bold"> <i class="fa fa-globe">&nbsp;  {{previewUser.blog}}</i> </p> </span> 
                          </div>
                          
                       
                    </div>
                    <div class="row justify-content-center mt-3 ">
                      <div class="col-lg-5 col-sm-9 ">
                        <div class="card shadow">
                          <div class="card-body shadow text-center p-1">
                            <p class="p-0 m-0 font-weight-bold"> {{previewUser.following}}</p> 
                           <p class="p-0 m-0 small">Followings</p> 
                          </div>
                        </div>
                      </div>
                      <div class="col-lg-5 col-sm-9">
                        <div class="card shadow">
                          <div class="card-body shadow text-center p-1">
                             <p class="p-0 m-0 font-weight-bold"> {{previewUser.followers}}</p> 
                           <p class="p-0 m-0 small">Followers</p> 
                          </div>
                        </div>
                      </div>
                  </div>
                  </div>
                </div>
                <div class="card-body">
                  
                  
                  <div class="card mb-3" v-if="previewUser.bio !==null">
                    <div class="card-body">
                      <p class="font-weight-bold p-0 m-0">Bio:</p> 
                      <p class="card-text bio">{{previewUser.bio}}.</p>
                    </div>
                  </div>


                  <div class="card mt-2 border-0" v-if="previewUserFollowers.length >0">
                    <span class="badge badge-default p-2  mb-3 col-lg-2 border dec-tag">Followers <i class="fa fa-arrow-down" aria-hidden="true"></i></span>
                    <div class="card-body p-0">
                      <div class="row justify-content-start section">
                      <div @click="preview(follower)" v-for="follower in previewUserFollowers " :key="follower.id" class="col-lg-4 ">
                        <div class="card list-card-2 mb-2">
                          <div class="card-body text-center p-1">
                              <div class="col-2 float-left p-0 mt-2 ml-2 m-0"> <img class="img-fluid rounded-circle" :src=follower.avatar_url alt=""></div>
                                <div class="col-9 float-left pl-2 pr-2 pt-0 m-0">
                                <p class="small text-left p-0 m-0 mt-2 font-weight-bold">{{follower.login}} </p>
                                <p class="role p-0 m-0 small" style="font-size:10px">{{(follower.html_url).slice(8)}}</p>                                  
                            </div>
                          </div>
                        </div>
                      </div>
                     
                  </div>
                    </div>
                  </div>

                   <div class="card mt-2 border-0 mt-3" v-if="previewUserRepos.length >0">
                    <span class="badge badge-default p-2  mb-3 col-lg-2 border dec-tag">Repositories <i class="fa fa-arrow-down" aria-hidden="true"></i></span>
                    <div class="card-body p-0">
                      <div class="row justify-content-start section">
                      <div  v-for="repo in previewUserRepos " :key="repo.id" class="col-lg-4 ">
                       <a :href="repo.html_url" target="blank">
                       <div class="card list-card-2 mb-2">
                          <div class="card-body text-center p-1">
                                <div class="col-12 float-left pl-2 pr-2 pt-0 m-0">
                                <p class="small text-left p-0 m-0 mt-2 font-weight-bold">{{repo.name}} </p>
                                <p class="role p-0 m-0 small" style="font-size:10px">{{repo.language}}</p>  
                                <span class="badge badge-success float-right border" v-if="repo.private == false">public</span>                                
                                <span class="badge badge-danger float-right border" v-else>{{repo.private}}</span> 
                                <span class="badge badge-info float-left border" v-if="repo.fork == true">forked</span>                                
                            </div>
                          </div>
                        </div>
                        </a> 
                      </div>
                     
                  </div>
                    </div>
                  </div>
                </div>
                
              </div>
            </div>





            <div class="col-sm-7 pt-0 previewEmpty" v-if="Object.keys(previewUser).length === 0">
           
            </div>





          </div>
        </div>
      </div>



        <div class="row p-lg-5" v-if="panel3 == true">
        <div class=" col-12  p-lg-5 fade-in">
          <div class="row">
            <div class="col-sm-6 not-found-img">
             
            </div>
            <div class="col-sm-5 ml-lg-4 mt-lg-5 mb-lg-5">
              <p class="intro-2 font-weight-bolder">Oops <br> {{errorMessage}}</p>
               <input @input="switchText" class="p-4 mt-4 mb-5 search-box border-0 col-12" type="text" placeholder="Enter Username">
            </div>

          

          </div>
        </div>
      </div>

      <a href="#" class="float shadow">
          <i class="fa fa-plus my-float"></i>
      </a>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Search',

  props: {
    msg: String
  },
   data(){
      return {
          rawData:[],
          result:[],
          users:[],
          loader: false,

          searchText1:'',
          searchText2:'',
          searchText3:'',

          panel1:true,
          panel2:false,
          panel3:false,

          previewUser:{},
          previewUserFollowers:[],
          previewUserRepos:[],


          paginate:{},
          current_page:1,
          per_page_items:5,
          total: 0,
          total_pages: 0,

          pre_page : 0,
          next_page : 0,

          errorMessage:''

      }
    },

    mounted(){
      
    },
    methods:{
      switchText(){
        this.panel1 = false;
        this.panel3 = false;
        this.panel2 = true;
        this.$refs.searchText2.$el.focus();
      },
     async getMe(){
        axios.get('https://api.github.com/search/users?q=tom', {
          headers: {
            authorization: 'none'
            // authorization: 'my secret token'
          }
        })
        .then((response => {
          console.log(response.data)
              this.result = response.data;
          }))
        .catch((error) => {
              console.log(error)
          })
      },
        async search(){
          this.loader = true
          let parameter = this.searchText2;
          await axios.get('https://api.github.com/search/users?q='+parameter, {
            headers: {
              authorization: 'none'
              // authorization: 'my secret token'
            }
          })  
          .then((response => {
           console.log(response.data.items)
            if (response.data.items.length > 0) {
               this.rawData = response.data.items
            } else {
               this.panel2 = !this.panel2
               this.panel3 = !this.panel3
            }   
          }))
          .catch((error) => {
                console.log(error)
                this.errorMessage = error.message
                this.panel2 = !this.panel2
                this.panel3 = !this.panel3
            })
          .finally(() => {
            this.paginator()  
            this.loader = false  
            })
      },

         preview(user){
           axios.get('https://api.github.com/users/'+user.login )  
          .then((response => {
            console.log(response.data)
               this.previewUser = response.data
            }))
          .catch((error) => {
                console.log(error)
                this.errorMessage = error.message
                this.panel2 = !this.panel2
                this.panel3 = !this.panel3
            })
          .finally(() => {
              console.log('finished')
               this.followers()
            })
      },
      followers(){
           axios.get('https://api.github.com/users/'+this.previewUser.login+'/followers' )  
          .then((response => {
            console.log(response)
               this.previewUserFollowers = response.data
            }))
          .catch((error) => {
                console.log(error)
                this.errorMessage = error.message
                this.panel2 = !this.panel2
                this.panel3 = !this.panel3
            })
          .finally(() => {
              console.log('followers')
              this.getRepos()
            })
      },
       getRepos(){
           axios.get('https://api.github.com/users/'+this.previewUser.login+'/repos' )  
          .then((response => {
            console.log(response)
               this.previewUserRepos = response.data
            }))
          .catch((error) => {
                console.log(error)
                this.errorMessage = error.message
                this.panel2 = !this.panel2
                this.panel3 = !this.panel3
            })
          .finally(() => {
              console.log('followers')
            })
      },
      paginator() {
        let items = this.rawData
        let current_page = this.current_page
        let per_page_items = this.per_page_items

         console.log('current_page'+current_page)
         console.log('per_page_items'+per_page_items)

        let page = current_page || 1,
        per_page = per_page_items || 10,
        offset = (page - 1) * per_page,

        paginatedItems = items.slice(offset).slice(0, per_page_items),
        total_pages = Math.ceil(items.length / per_page);
        
        this.result = paginatedItems
        this.current_page = page
        this. pre_page = page - 1 ? page - 1 : null,
        this.next_page = (total_pages > page) ? page + 1 : null,
        this.total = items.length
        this.total_pages = total_pages

        
      },
       next() {
        let items = this.rawData
        let current_page = this.current_page + 1
        let per_page_items = this.per_page_items

          console.log('current_page'+current_page)
         console.log('per_page_items'+per_page_items)
         console.log('total'+ this.total_pages)

        let page = current_page || 1,
        per_page = per_page_items || 10,
        offset = (page - 1) * per_page,

        paginatedItems = items.slice(offset).slice(0, per_page_items),
        total_pages = Math.ceil(items.length / per_page);

        this.result = paginatedItems
        this.current_page = page
        this. pre_page = page - 1 ? page - 1 : null,
        this.next_page = (total_pages > page) ? page + 1 : null,
        this.total = items.length
        this.total_pages = total_pages

       
      },
      prev() {
        let items = this.rawData
        let current_page = this.current_page - 1
        let per_page_items = this.per_page_items
        
       console.log('current_page'+current_page)
         console.log('per_page_items'+per_page_items)
         console.log('total'+ this.total_pages)

        let page = current_page || 1,
        per_page = per_page_items || 10,
        offset = (page - 1) * per_page,

        paginatedItems = items.slice(offset).slice(0, per_page_items),
        total_pages = Math.ceil(items.length / per_page);

        this.result = paginatedItems
        this.current_page = page
        this. pre_page = page - 1 ? page - 1 : null,
        this.next_page = (total_pages > page) ? page + 1 : null,
        this.total = items.length
        this.total_pages = total_pages

       
      }
      
   }
}
</script>

<style>
@import url('./style.css');
  
</style>