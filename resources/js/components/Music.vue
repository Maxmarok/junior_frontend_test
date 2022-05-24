<template>
    <div class="page__center wide">
        <div class="page__row page__row_head" v-if="!mobile">
            <div class="page__col col__header d-flex flex-column flex-sm-row justify-content-space-between">
                <div class="mt-4 mt-sm-0">
                    <div class="page__hello h5" v-if="user" v-html="user.name+','"/>
                    <div class="page__welcome h2">Привет 👋</div>
                </div>

                <div class="mt-4 mt-sm-0 page__status">
                    <p class="mb-2">
                        <svg width="14" height="14" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <circle class="color" cx="7" cy="7" r="6.5" fill="#7FBA7A"/>
                        </svg>

                        <span class="color-gray ml-2 align-middle">На продвижении</span>
                    </p>
                    <p>
                        <svg width="14" height="14" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
                            <circle class="color" cx="7" cy="7" r="6.5" fill="#FF4242"/>
                        </svg>

                        <span class="color-gray ml-2 align-middle">Есть заявки</span>
                    </p>
                </div>
            </div>
        </div>

        <div class="page__row" v-if="mobile && items.length">
            <div class="d-flex flex-row align-items-center justify-content-between mobile-btn">
                <h1>Мои треки</h1>
                <img :src="url + 'img/icon_plus_main.svg'" @click="openMusicModal('music-modal')" class="mb-2" />
            </div>
        </div>

        <div class="page__row" v-if="mobile && items.length">
             <div class="page__panel">
                <p>
                    <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle class="color" cx="6" cy="6" r="5.5" fill="#7FBA7A"/>
                    </svg>

                    <span>На продвижении</span>
                </p>
                <p>
                    <svg width="12" height="12" viewBox="0 0 12 12" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <circle class="color" cx="6" cy="6" r="5.5" fill="#FF4242"/>
                    </svg>

                    <span>Есть заявки</span>
                </p>
            </div>
        </div>



        <div class="page__row page__row_border">
            <div class="page__col">
                <div class="page__banner banner" v-if="!items.length">
                    <h2 class="banner__title">Загрузи свой первый трек</h2>
                    <p class="banner__descr">
                        Твои будущие фанаты ждут! Жми на&nbsp;кнопку «добавить трек» и переходи к продвижению прямо сейчас.
                    </p>
                    <div class="products__item" @click="openMusicModal('music-modal')">
                        <button class="btn banner__btn">Добавить трек</button>
                    </div>
                </div>
                <div class="products__grid" v-else>
                    <div class="products__item" @click="openMusicModal('music-modal')" v-if="!mobile">
                        <div class="products__preview new"></div>
                        <div class="products__details">
                            <div class="products__title title">Добавить трек</div>
                        </div>
                    </div>
                    <div class="products__item" v-for="item in items" :key="item.id">
                      <div class="products__cover">
                        <img :src="item.image" alt="Фото обложки">
                      </div>
                      <div class="products__info">
                        <h3 class="products__title">{{item.author}}</h3>
                        <span class="products__song">{{item.title}}</span>
                      </div>
                    </div>
                </div>
            </div>
        </div>

        <b-modal id="music-modal" centered hide-footer>
            <div class="modal-center d-flex flex-column text-center mx-auto">
                <div class="form-block">
                    <input type="text" class="form-control" placeholder="Ссылка на звук в TikTok" v-model="val" required="" />
                    <p class="form-tip text-danger" v-if="error" v-html="error" />
                    <button class="btn btn-lg btn-primary btn-block my-4" @click="getMusic(val)" :disabled="!val" v-if="!waiting" v-html="val ? 'Найти трек' : 'Введите ссылку на трек'" />
                    <div class="loading" :class="{active: waiting}" />
                    <p class="form-tip" v-if="waiting" v-html="'Ищем трек, это займет от 5 до 10 секунд'" />
                </div>
            </div>
        </b-modal>
        <b-modal id="music-info-modal" centered hide-footer>
          <div class="modal-center d-flex flex-column text-center mx-auto">
            <div class="form-block">
              <div class="form-img">
                <img :src="coverThumb" alt="Фото обложки">
              </div>
              <label class="form-label">
                <span>Ссылка на звук в TikTok</span>
                <input type="text" class="form-control" placeholder="Ссылка на звук в TikTok" v-model="val" required="" />
              </label>
              <label class="form-label">
                <span>Название</span>
                <input type="text" class="form-control" placeholder="Название трека" v-model="title" required="" />
              </label>
              <label class="form-label">
                <span>Исполнитель</span>
                <input type="text" class="form-control" placeholder="Исполнитель трека" v-model="author" required="" />
              </label>
              <label class="form-label">
                <span>Альбом</span>
                <input type="text" class="form-control" placeholder="Альбом трека" v-model="album" required="" />
              </label>
              <p class="form-tip text-danger" v-if="error" v-html="error" />
              <button class="btn btn-lg btn-primary btn-block my-4" @click="addMusic"  v-if="!waiting" :disabled="!val || !title">Добавить</button>
              <div class="loading" :class="{active: waiting}" />
              <p class="form-tip" v-if="waiting" v-html="'Добавляем трек, это займет от 5 до 10 секунд'" />
            </div>
          </div>
        </b-modal>
    </div>
</template>

<script>
    import axios from "axios"
    import {mapGetters} from "vuex";
    import { GET_MUSIC_LIST, GET_MUSIC, ADD_MUSIC } from '../api-routes';

    export default {
        components: { },
        name: "Music",

        data() {
            return {
                editing: false,
                val: this.value,
                music: null,
                id: null,
                title: null,
                author: null,
                album: null,
                coverThumb: null,
                error: null,
                waiting: false,
                items: [],
            }
        },

        mounted() {
            this.getMusicList();
        },

      computed: {
            ...mapGetters(['user', 'userAuthorized', 'url', 'tablet', 'mobile']),
        },

        methods: {
            openMusicModal(id) {
                this.waiting = this.error = false;
                this.$bvModal.show(id);
            },

            getMusicList() {
                axios.get(GET_MUSIC_LIST).then(response => {
                    this.items = response.data;
                });
            },

            getMusic(url) {
                this.waiting = true;
                this.error = null;

                axios.post(GET_MUSIC, {url: url}).then(response => {
                    this.waiting = false;

                    /* TO DO */
                    const {album, coverThumb, title, authorName, id} = response.data.music;
                    this.album = album;
                    this.coverThumb = coverThumb;
                    this.title = title;
                    this.author = authorName;
                    this.id = id;

                    this.$bvModal.hide('music-modal');
                    this.openMusicModal('music-info-modal');

                    this.error = null;
                }).catch(error => {
                    console.log(error);
                    this.waiting = false;
                    if(error !== undefined) {
                        this.error = 'Не удалось найти трек, попробуйте еще раз';
                    }
                });
            },

            addMusic() {
              this.waiting = true;
              this.error = null;

              axios.post(ADD_MUSIC,
                {
                  title: this.title,
                  author: this.author,
                  album: this.album,
                  image: this.coverThumb,
                  url: this.val
                  })
                .then(response => {
                this.waiting = false;
                this.error = null;

                if (response.status === 200) {
                  this.getMusicList();
                  this.val = null;
                }

              }).catch(error => {
                console.log(error);
                this.waiting = false;
                if(error !== undefined) {
                  this.error = 'Не удалось добавить трек, попробуйте еще раз';
                }
              }).finally( () => {
                this.$bvModal.hide('music-info-modal');
              });
            }
        }

    }
</script>
