<template>
  <div class="container-fluid wrapper bg-white p-lg-3 p-sm-0">
    <div class="row p-lg-5 h-100 m-0 intro" v-if="panel1 == true">
      <div class=" col-12 intro-container p-lg-5 fade-in">
        <div class="row m-0">
          <div class="col-sm-5 col-md-12 ml-lg-4 mt-lg-5 mb-lg-5">
            <div class="row m-0 justify-content-center">
              <img
                src="../assets/github.png"
                class="img-fluid rounded-circle col-md-11 col-lg-3 mb-3 mt-3"
                alt=""
              />
            </div>
            <div class="row m-0 justify-content-center">
              <p class="intro-1 font-weight-bolder col-lg-12 text-center">
                Find GitHub
              </p>
              <p class="intro-2 font-weight-bolder">User</p>
            </div>
            <div class="row m-0 justify-content-center">
              <button
                @click="switchText"
                class="p-3 mt-4 mb-5 search-box main-btn border-0 col-6 col-lg-2  text-center"
              >
                <i class="fa fa-arrow-right" aria-hidden="true"></i>
              </button>
            </div>
          </div>

          <div class="col-sm-6 panel-img"></div>
        </div>
      </div>
    </div>

    <div
      class="row pl-lg-5 pl-sm-0 pr-sm-0 pr-lg-5 pt-1 m-0 h-100"
      v-if="panel2 == true"
    >
      <div
        class=" col-lg-12 col-sm-12 col-md-12 pl-lg-3 pr-lg-3 pt-1 fade-in ppanel"
      >
        <div class="row m-0 p-md-4 p-0">
          <div
            class=" col-lg-5  col-sm-12 col-md-12 pl-lg-4 pb-lg-2 pl-sm-0 pr-sm-0"
          >
            <div class="form-row p-md-2">
              <div
                class="form-group col-lg-8 col-md-12 col-sm-12  mb-sm-0 p-0 "
              >
                <input
                  v-model="searchText2"
                  @keyup.enter="search"
                  autofocus
                  class="form-control form-control-lg col-lg-12 col-md-11 col-sm-11 float-left shadow search search-container"
                  type="text"
                  placeholder="Enter a username"
                />
                <button
                  @click="search"
                  :disabled="searchText2 == ''"
                  type="button"
                  class="btn btn-primary p-md-2 col-sm-2 col-md-1 btn-lg float-left small-btn d-lg-none d-sm-block"
                >
                  <i class="fa fa-search"></i>
                </button>
              </div>
              <div class="form-group col-md-2 col-lg-4 d-none d-lg-block">
                <select
                  v-model="per_page_items"
                  class="form-control form-control-lg col-lg-4 mr-2 pl-2  text-sm float-left shadow  "
                >
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
                <button
                  @click="search"
                  :disabled="searchText2 == ''"
                  type="button"
                  class="btn btn-primary btn-lg"
                >
                  <i class="fa fa-search"></i>
                </button>
              </div>
            </div>

            <div class="result-list">
              <div
                v-if="loader === true"
                class="row justify-content-center m-0 mt-5"
              >
                <div class="db-spinner mt-5"></div>
              </div>

              <div
                data-aos="fade-in"
                @click="preview(user)"
                v-for="user in result"
                :key="user.id"
                class="card col-lg-11 col-md-12 col-sm-12  list-card mb-2"
              >
                <div class="card-body p-2">
                  <div class="row m-0">
                    <div class="col-2 p-0 m-0">
                      <img
                        class="img-fluid rounded-circle"
                        :src="user.avatar_url"
                        alt=""
                      />
                    </div>
                    <div class="col-10 pl-2 pr-2 pt-0 m-0">
                      <p class="name p-0 m-0 mt-2 font-weight-bold">
                        {{ user.login }}
                      </p>
                      <p class="role p-0 m-0 small">
                        {{ user.html_url.slice(8) }}
                      </p>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="row justify-content-center m-0">
              <nav
                aria-label=""
                class=" mb-0  mr-lg-5"
                v-if="rawData.length > 0"
              >
                <ul class="pagination">
                  <li class="page-item   mr-2" :disabled="current_page == 1">
                    <button
                      @click="prev()"
                      class="page-link"
                      :disabled="current_page == 1"
                      href="#"
                      tabindex="-1"
                    >
                      <i class="fa fa-arrow-left" aria-hidden="true"></i> Prev
                    </button>
                  </li>
                  <li class="page-item   mr-2" :disabled="current_page == 1">
                    <button class="page-link" disabled>
                      {{ rawData.length }}
                    </button>
                  </li>
                  <li
                    class="page-item  ml-2"
                    :disabled="current_page == total_pages"
                  >
                    <button
                      @click="next()"
                      class="page-link "
                      :disabled="current_page == total_pages"
                      href="#"
                    >
                      Next <i class="fa fa-arrow-right" aria-hidden="true"></i>
                    </button>
                  </li>
                </ul>
              </nav>
            </div>
          </div>

          <div
            class="col-lg-7 col-md-12 col-sm-12 p-0 preview"
            v-if="Object.keys(previewUser).length !== 0"
          >
            <div
              class="card border-0 p-0"
              v-if="Object.keys(previewUser).length !== 0"
            >
              <div class="row justify-content-center m-0">
                <div class="col-4 p-0 m-0">
                  <img
                    class="img-fluid rounded-circle shadow"
                    :src="previewUser.avatar_url"
                    alt=""
                  />
                  <a
                    href="#"
                    class="btn btn-default font-weight-bold border p-1 mt-2 followership-sm"
                    >{{ previewUser.following }} Followings</a
                  >
                  <a
                    href="#"
                    class="btn btn-default font-weight-bold border p-1 mt-2 followership-sm"
                    >{{ previewUser.followers }} Followers</a
                  >
                </div>
                <div class="col-7 p-0 m-0">
                  <div
                    class="row justify-content-start pl-5 pr-5 pt-3 font-weight-bold"
                  >
                    <p
                      class="card-title text-center font-weight-bold mb-0 h2-lg ml-2 userName"
                      v-if="previewUser.name !== null"
                    >
                      <a :href="previewUser.html_url" target="blank">{{
                        previewUser.name
                      }}</a>
                    </p>
                    <p
                      class="card-title text-center font-weight-bold mb-0 h2-lg"
                      v-else
                    >
                      <a :href="previewUser.html_url" target="blank">{{
                        previewUser.login
                      }}</a>
                    </p>
                    <!-- <p class="small text-center mb-0 mt-2"><i class="fa fa-at"></i> {{(previewUser.html_url)}}</p> -->
                    <div
                      class="card-body  p-1 col-12"
                      v-if="previewUser.company !== null"
                    >
                      <span class="badge badge-light"
                        ><p class="p-0 m-0 font-weight-bold tags">
                          <i class="fa fa-building"
                            >&nbsp; {{ previewUser.company }}</i
                          >
                        </p>
                      </span>
                    </div>
                    <div
                      class="card-body  p-1 col-12"
                      v-if="previewUser.location !== null"
                    >
                      <span class="badge badge-light"
                        ><p class="p-0 m-0 font-weight-bold tags">
                          <i class="fa fa-map-marker"
                            >&nbsp; {{ previewUser.location }}</i
                          >
                        </p>
                      </span>
                    </div>
                    <div
                      class="card-body  p-1 col-12"
                      v-if="previewUser.email !== null"
                    >
                      <span class="badge badge-light"
                        ><p class="p-0 m-0 font-weight-bold tags">
                          <i class="fa fa-at">&nbsp; {{ previewUser.email }}</i>
                        </p>
                      </span>
                    </div>
                    <div
                      class="card-body  p-1 col-12"
                      v-if="previewUser.twitter_username !== null"
                    >
                      <span class="badge badge-light"
                        ><p class="p-0 m-0 font-weight-bold tags">
                          <i class="fa fa-twitter"
                            >&nbsp; {{ previewUser.twitter_username }}</i
                          >
                        </p>
                      </span>
                    </div>
                    <div
                      class="card-body  p-1 col-12"
                      v-if="previewUser.blog !== (null || '')"
                    >
                      <span class="badge badge-light"
                        ><p class="p-0 m-0 font-weight-bold tags">
                          <i class="fa fa-globe"
                            >&nbsp; {{ previewUser.blog }}</i
                          >
                        </p>
                      </span>
                    </div>
                  </div>
                  <div
                    class="row justify-content-center mt-3 followership-lg m-0"
                  >
                    <div class="col-lg-5 col-sm-9 ">
                      <div class="card shadow">
                        <div class="card-body shadow text-center p-1">
                          <p class="p-0 m-0 font-weight-bold">
                            {{ previewUser.following }}
                          </p>
                          <p class="p-0 m-0 small">Followings</p>
                        </div>
                      </div>
                    </div>
                    <div class="col-lg-5 col-sm-9">
                      <div class="card shadow">
                        <div class="card-body shadow text-center p-1">
                          <p class="p-0 m-0 font-weight-bold">
                            {{ previewUser.followers }}
                          </p>
                          <p class="p-0 m-0 small">Followers</p>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
              <div class="card-body">
                <div class="card mb-3" v-if="previewUser.bio !== null">
                  <div class="card-body">
                    <p class="font-weight-bold p-0 m-0">Bio:</p>
                    <p class="card-text bio">{{ previewUser.bio }}.</p>
                  </div>
                </div>

                <div
                  class="card mt-2 border-0"
                  v-if="previewUserFollowers.length > 0"
                >
                  <span
                    class="badge badge-default p-2  mb-3 col-lg-2 border dec-tag"
                    >Followers
                    <i class="fa fa-arrow-down" aria-hidden="true"></i
                  ></span>
                  <div class="card-body p-0">
                    <div class="row justify-content-start section">
                      <div
                        @click="previewFollower(follower)"
                        data-toggle="modal"
                        data-target="#exampleModal"
                        v-for="follower in previewUserFollowers"
                        :key="follower.id"
                        class="col-lg-4 "
                      >
                        <div class="card list-card-2 mb-2">
                          <div class="card-body text-center p-1">
                            <div class="col-2 float-left p-0 mt-2  m-0">
                              <img
                                class="img-fluid rounded-circle"
                                :src="follower.avatar_url"
                                alt=""
                              />
                            </div>
                            <div class="col-9 float-left pl-2 pr-2 pt-0 m-0">
                              <p
                                class="small text-left p-0 m-0 mt-2 font-weight-bold"
                              >
                                {{ follower.login }}
                              </p>
                              <p
                                class="role p-0 m-0 small"
                                style="font-size:10px"
                              >
                                {{ follower.html_url.slice(8) }}
                              </p>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>

                <div
                  class="card mt-2 border-0 mt-3"
                  v-if="previewUserRepos.length > 0"
                >
                  <span
                    class="badge badge-default p-2  mb-3 col-lg-2 border dec-tag"
                    >Repositories
                    <i class="fa fa-arrow-down" aria-hidden="true"></i
                  ></span>
                  <div class="card-body p-0">
                    <div class="row justify-content-start section">
                      <div
                        v-for="repo in previewUserRepos"
                        :key="repo.id"
                        class="col-lg-4 "
                      >
                        <a :href="repo.html_url" target="blank">
                          <div class="card list-card-2 mb-2">
                            <div class="card-body text-center p-1">
                              <div class="col-12 float-left pl-2 pr-2 pt-0 m-0">
                                <p
                                  class="small text-left p-0 m-0 mt-2 font-weight-bold"
                                >
                                  {{ repo.name }}
                                </p>
                                <p
                                  class="role p-0 m-0 small"
                                  style="font-size:10px"
                                >
                                  {{ repo.language }}
                                </p>
                                <span
                                  class="badge badge-success float-right border"
                                  v-if="repo.private == false"
                                  >public</span
                                >
                                <span
                                  class="badge badge-danger float-right border"
                                  v-else
                                  >{{ repo.private }}</span
                                >
                                <span
                                  class="badge badge-info float-left border"
                                  v-if="repo.fork == true"
                                  >forked</span
                                >
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

          <!-- Modal for mobile preview -->
          <div
            class="modal fade"
            data-backdrop="static"
            id="exampleModal"
            tabindex="-1"
            aria-labelledby="exampleModalLabel"
            aria-hidden="true"
          >
            <div class="modal-dialog modal-dialog-scrollable">
              <div class="modal-content">
                <div class="modal-header pb-0 pt-0 border-0">
                  <button
                    type="button"
                    class="close"
                    data-dismiss="modal"
                    aria-label="Close"
                  >
                    <span aria-hidden="true">&times;</span>
                  </button>
                </div>
                <div class="modal-body p-0">
                  <div class="row justify-content-center m-0">
                    <div class="col-4 p-0 m-0">
                      <img
                        class="img-fluid rounded-circle shadow"
                        :src="previewUser.avatar_url"
                        alt=""
                      />
                      <a
                        href="#"
                        class="btn btn-default font-weight-bold border p-1 mt-2 followership-sm"
                        >{{ previewUser.following }} Followings</a
                      >
                      <a
                        href="#"
                        class="btn btn-default font-weight-bold border p-1 mt-2 followership-sm"
                        >{{ previewUser.followers }} Followers</a
                      >
                    </div>
                    <div class="col-7 p-0 m-0">
                      <div
                        class="row justify-content-start pl-5 pr-5 pt-3 font-weight-bold"
                      >
                        <p
                          class="card-title text-center font-weight-bold mb-0 h2-lg ml-2 userName"
                          v-if="previewUser.name !== null"
                        >
                          <a :href="previewUser.html_url" target="blank">{{
                            previewUser.name
                          }}</a>
                        </p>
                        <p
                          class="card-title text-center font-weight-bold mb-0 h2-lg"
                          v-else
                        >
                          <a :href="previewUser.html_url" target="blank">{{
                            previewUser.login
                          }}</a>
                        </p>
                        <!-- <p class="small text-center mb-0 mt-2"><i class="fa fa-at"></i> {{(previewUser.html_url)}}</p> -->
                        <div
                          class="card-body  p-1 col-12"
                          v-if="previewUser.company !== null"
                        >
                          <span class="badge badge-light"
                            ><p class="p-0 m-0 font-weight-bold tags">
                              <i class="fa fa-building"
                                >&nbsp; {{ previewUser.company }}</i
                              >
                            </p>
                          </span>
                        </div>
                        <div
                          class="card-body  p-1 col-12"
                          v-if="previewUser.location !== null"
                        >
                          <span class="badge badge-light"
                            ><p class="p-0 m-0 font-weight-bold tags">
                              <i class="fa fa-map-marker"
                                >&nbsp; {{ previewUser.location }}</i
                              >
                            </p>
                          </span>
                        </div>
                        <div
                          class="card-body  p-1 col-12"
                          v-if="previewUser.email !== null"
                        >
                          <span class="badge badge-light"
                            ><p class="p-0 m-0 font-weight-bold tags">
                              <i class="fa fa-at"
                                >&nbsp; {{ previewUser.email }}</i
                              >
                            </p>
                          </span>
                        </div>
                        <div
                          class="card-body  p-1 col-12"
                          v-if="previewUser.twitter_username !== null"
                        >
                          <span class="badge badge-light"
                            ><p class="p-0 m-0 font-weight-bold tags">
                              <i class="fa fa-twitter"
                                >&nbsp; {{ previewUser.twitter_username }}</i
                              >
                            </p>
                          </span>
                        </div>
                        <div
                          class="card-body  p-1 col-12"
                          v-if="previewUser.blog !== (null || '')"
                        >
                          <span class="badge badge-light"
                            ><p class="p-0 m-0 font-weight-bold tags">
                              <i class="fa fa-globe"
                                >&nbsp; {{ previewUser.blog }}</i
                              >
                            </p>
                          </span>
                        </div>
                      </div>
                      <div
                        class="row justify-content-center mt-3 followership-lg m-0"
                      >
                        <div class="col-lg-5 col-sm-9 ">
                          <div class="card shadow">
                            <div class="card-body shadow text-center p-1">
                              <p class="p-0 m-0 font-weight-bold">
                                {{ previewUser.following }}
                              </p>
                              <p class="p-0 m-0 small">Followings</p>
                            </div>
                          </div>
                        </div>
                        <div class="col-lg-5 col-sm-9">
                          <div class="card shadow">
                            <div class="card-body shadow text-center p-1">
                              <p class="p-0 m-0 font-weight-bold">
                                {{ previewUser.followers }}
                              </p>
                              <p class="p-0 m-0 small">Followers</p>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                  <div class="card-body">
                    <div class="card mb-3" v-if="previewUser.bio !== null">
                      <div class="card-body">
                        <p class="font-weight-bold p-0 m-0">Bio:</p>
                        <p class="card-text bio">{{ previewUser.bio }}.</p>
                      </div>
                    </div>

                    <div
                      class="card mt-2 border-0"
                      v-if="previewUserFollowers.length > 0"
                    >
                      <span
                        class="badge badge-default p-2  mb-3 col-lg-2 border dec-tag"
                        >Followers
                        <i class="fa fa-arrow-down" aria-hidden="true"></i
                      ></span>
                      <div class="card-body p-0">
                        <div class="row justify-content-start section">
                          <div
                            @click="previewFollower(follower)"
                            v-for="follower in previewUserFollowers"
                            :key="follower.id"
                            class="col-lg-4 "
                          >
                            <div class="card list-card-2 mb-2">
                              <div class="card-body text-center p-1">
                                <div class="col-2 float-left p-0 mt-2  m-0">
                                  <img
                                    class="img-fluid rounded-circle"
                                    :src="follower.avatar_url"
                                    alt=""
                                  />
                                </div>
                                <div
                                  class="col-9 float-left pl-2 pr-2 pt-0 m-0"
                                >
                                  <p
                                    class="small text-left p-0 m-0 mt-2 font-weight-bold"
                                  >
                                    {{ follower.login }}
                                  </p>
                                  <p
                                    class="role p-0 m-0 small"
                                    style="font-size:10px"
                                  >
                                    {{ follower.html_url.slice(8) }}
                                  </p>
                                </div>
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>

                    <div
                      class="card mt-2 border-0 mt-3"
                      v-if="previewUserRepos.length > 0"
                    >
                      <span
                        class="badge badge-default p-2  mb-3 col-lg-2 border dec-tag"
                        >Repositories
                        <i class="fa fa-arrow-down" aria-hidden="true"></i
                      ></span>
                      <div class="card-body p-0">
                        <div class="row justify-content-start section">
                          <div
                            v-for="repo in previewUserRepos"
                            :key="repo.id"
                            class="col-lg-4 "
                          >
                            <a :href="repo.html_url" target="blank">
                              <div class="card list-card-2 mb-2">
                                <div class="card-body text-center p-1">
                                  <div
                                    class="col-12 float-left pl-2 pr-2 pt-0 m-0"
                                  >
                                    <p
                                      class="small text-left p-0 m-0 mt-2 font-weight-bold"
                                    >
                                      {{ repo.name }}
                                    </p>
                                    <p
                                      class="role p-0 m-0 small"
                                      style="font-size:10px"
                                    >
                                      {{ repo.language }}
                                    </p>
                                    <span
                                      class="badge badge-success float-right border"
                                      v-if="repo.private == false"
                                      >public</span
                                    >
                                    <span
                                      class="badge badge-danger float-right border"
                                      v-else
                                      >{{ repo.private }}</span
                                    >
                                    <span
                                      class="badge badge-info float-left border"
                                      v-if="repo.fork == true"
                                      >forked</span
                                    >
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
                <div class="modal-footer p-0 border-0">
                  <button
                    type="button"
                    class="btn btn-primary btn-sm p-1 small"
                    data-dismiss="modal"
                  >
                    Close
                  </button>
                </div>
              </div>
            </div>
          </div>

          <div
            class="col-lg-7 col-md-12 col-sm-12 pt-0 previewEmpty"
            v-if="Object.keys(previewUser).length === 0"
          ></div>
        </div>
      </div>
    </div>

    <div class="row h-100 m-0 justify-content-center" v-if="panel3 == true">
      <div class=" col-12 fade-in">
        <div class="row m-0 justify-content-center">
          <div class="col-sm-12 col-lg-4  not-found-img"></div>
          <div class="col-lg-5 col-sm-12 ml-lg-4 mt-lg-5 mb-lg-5">
            <p class=" text-center small font-weight-bolder">
              Oops <br />
              <span class="small">{{ errorMessage }}</span>
            </p>
            <div class="row m-0 justify-content-center">
              <button
                @click="switchText"
                class="p-3 mt-4 mb-5 search-box main-btn border-0 col-6 text-center"
              >
                <i class="fa fa-arrow-left" aria-hidden="true"></i>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>

    <a data-toggle="modal" data-target="#profile" class="float shadow">
      <i class="fa fa-id-card my-float"></i>
    </a>

    <button
      type="button"
      class="btn btn-primary d-none"
      id="modal"
      data-toggle="modal"
      data-target="#exampleModal"
    >
      Launch mobile view modal
    </button>

    <!-- Personal Modal -->
    <div
      class="modal fade"
      id="profile"
      tabindex="-1"
      aria-labelledby="profileLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-body p-0">
            <div class="row justify-content-center">
              <div class="card col-12 p-0">
                <img
                  class="card-img-top"
                  :src="personal.avatar_url"
                  alt="Bologna"
                />
                <div class="card-body text-center">
                  <img
                    class="avatar rounded-circle"
                    :src="personal.avatar_url"
                    alt="Bologna"
                  />{{ previewUser.location }}
                  <h4 class="card-title">{{ personal.name }}</h4>
                  <h6 class="card-subtitle mb-2 text-muted">
                    {{ personal.company }}
                  </h6>
                  <h6 class="card-subtitle mb-2 text-muted small">
                    <i class="fa fa-map-marker" aria-hidden="true"></i>
                    {{ personal.location }}
                  </h6>

                  <a
                    :href="personal.html_url"
                    target="blank"
                    class="btn btn-primary btn-block text-white"
                    ><i class="fa fa-github" aria-hidden="true"></i
                  ></a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import $ from "jquery";

export default {
  name: "Search",

  props: {
    msg: String,
  },
  data() {
    return {
      personal: {},
      rawData: [],
      result: [],
      users: [],
      loader: false,

      showList: true,
      showPreview: true,

      searchText1: "",
      searchText2: "",
      searchText3: "",

      panel1: true,
      panel2: false,
      panel3: false,

      previewUser: {},
      previewUserFollowers: [],
      previewUserRepos: [],

      paginate: {},
      current_page: 1,
      per_page_items: 5,
      total: 0,
      total_pages: 0,

      pre_page: 0,
      next_page: 0,

      errorMessage: "",
    };
  },

  mounted() {
    this.getMe();
  },
  methods: {
    switchText() {
      this.panel1 = false;
      this.panel3 = false;
      this.panel2 = true;
      // this.$refs.searchText2.$el.focus();
    },
    async getMe() {
      axios
        .get("https://api.github.com/users/agumaxwellforcode", {
          headers: {
            authorization: "none",
            // authorization: 'my secret token'
          },
        })
        .then((response) => {
          console.log(response.data);
          this.personal = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async search() {
      this.loader = true;
      let parameter = this.searchText2;
      await axios
        .get("https://api.github.com/search/users?q=" + parameter, {
          headers: {
            authorization: "none",
            // authorization: 'my secret token'
          },
        })
        .then((response) => {
          console.log(response.data.items);
          if (response.data.items.length > 0) {
            this.rawData = response.data.items;
          } else {
            this.errorMessage = "No users match the username";
            this.panel2 = !this.panel2;
            this.panel3 = !this.panel3;
          }
        })
        .catch((error) => {
          console.log(error);
          this.errorMessage = error.message;
          this.panel2 = !this.panel2;
          this.panel3 = !this.panel3;
        })
        .finally(() => {
          this.paginator();
          this.loader = false;
        });
    },

    preview(user) {
      axios
        .get("https://api.github.com/users/" + user.login)
        .then((response) => {
          console.log(response.data);
          this.previewUser = response.data;
        })
        .catch((error) => {
          console.log(error);
          this.errorMessage = error.message;
           $("#modal").click();
          this.panel2 = !this.panel2;
          this.panel3 = !this.panel3;
        })
        .finally(() => {
          if (window.innerWidth <= 768) {
            console.log("Mobile");
            $("#modal").click();
          } else {
            console.log("Large screen");
          }

          console.log("finished");
          this.followers();
        });
    },
     previewFollower(user) {
      axios
        .get("https://api.github.com/users/" + user.login)
        .then((response) => {
          console.log(response.data);
          this.previewUser = response.data;
        })
        .catch((error) => {
          console.log(error);
          this.errorMessage = error.message;
           $("#modal").click();
          this.panel2 = !this.panel2;
          this.panel3 = !this.panel3;
        })
        .finally(() => {
          if (window.innerWidth <= 768) {
            console.log("Mobile");
            // $("#modal").click();
          } else {
            console.log("Large screen");
          }

          console.log("finished");
          this.followers();
        });
    },
    followers() {
      axios
        .get(
          "https://api.github.com/users/" +
            this.previewUser.login +
            "/followers"
        )
        .then((response) => {
          console.log(response);
          this.previewUserFollowers = response.data;
        })
        .catch((error) => {
          console.log(error);
          this.errorMessage = error.message;
          
          this.panel2 = !this.panel2;
          this.panel3 = !this.panel3;
           $("#modal").click();
        })
        .finally(() => {
          console.log("followers");
          this.getRepos();
        });
    },
    getRepos() {
      axios
        .get(
          "https://api.github.com/users/" + this.previewUser.login + "/repos"
        )
        .then((response) => {
          console.log(response);
          this.previewUserRepos = response.data;
        })
        .catch((error) => {
          console.log(error);
          this.errorMessage = error.message;
          this.panel2 = !this.panel2;
          this.panel3 = !this.panel3;
           $("#modal").click();
        })
        .finally(() => {
          console.log("followers");
        });
    },
    paginator() {
      let items = this.rawData;
      let current_page = this.current_page;
      let per_page_items = this.per_page_items;

      console.log("current_page" + current_page);
      console.log("per_page_items" + per_page_items);

      let page = current_page || 1,
        per_page = per_page_items || 10,
        offset = (page - 1) * per_page,
        paginatedItems = items.slice(offset).slice(0, per_page_items),
        total_pages = Math.ceil(items.length / per_page);

      this.result = paginatedItems;
      this.current_page = page;
      (this.pre_page = page - 1 ? page - 1 : null),
        (this.next_page = total_pages > page ? page + 1 : null),
        (this.total = items.length);
      this.total_pages = total_pages;
    },
    next() {
      let items = this.rawData;
      let current_page = this.current_page + 1;
      let per_page_items = this.per_page_items;

      console.log("current_page" + current_page);
      console.log("per_page_items" + per_page_items);
      console.log("total" + this.total_pages);

      let page = current_page || 1,
        per_page = per_page_items || 10,
        offset = (page - 1) * per_page,
        paginatedItems = items.slice(offset).slice(0, per_page_items),
        total_pages = Math.ceil(items.length / per_page);

      this.result = paginatedItems;
      this.current_page = page;
      (this.pre_page = page - 1 ? page - 1 : null),
        (this.next_page = total_pages > page ? page + 1 : null),
        (this.total = items.length);
      this.total_pages = total_pages;
    },
    prev() {
      let items = this.rawData;
      let current_page = this.current_page - 1;
      let per_page_items = this.per_page_items;

      console.log("current_page" + current_page);
      console.log("per_page_items" + per_page_items);
      console.log("total" + this.total_pages);

      let page = current_page || 1,
        per_page = per_page_items || 10,
        offset = (page - 1) * per_page,
        paginatedItems = items.slice(offset).slice(0, per_page_items),
        total_pages = Math.ceil(items.length / per_page);

      this.result = paginatedItems;
      this.current_page = page;
      (this.pre_page = page - 1 ? page - 1 : null),
        (this.next_page = total_pages > page ? page + 1 : null),
        (this.total = items.length);
      this.total_pages = total_pages;
    },
    toggleList() {
      this.showList = !this.showList;
      this.showPreview = !this.showPreview;
    },
  },
};
</script>

<style>
@import url("./style.css");
</style>
