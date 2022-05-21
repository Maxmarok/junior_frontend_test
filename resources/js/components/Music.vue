<template>
    <div class="page__center wide">
        <div class="page__row page__row_head" v-if="!mobile">
            <div class="page__col col__header d-flex flex-column flex-sm-row justify-content-space-between">
                <div class="mt-4 mt-sm-0">
                    <div class="page__hello h5" v-if="user" v-html="user.name+','"/>
                    <div class="page__welcome h2">Привет 👋</div>
                </div>

                <div class="mt-4 mt-sm-0">
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

        <div class="page__row" v-if="mobile">
            <div class="d-flex flex-row align-items-center justify-content-between">
                <h1>Мои треки</h1>
                <img :src="url + 'img/icon_plus_main.svg'" @click="openMusicModal" class="mb-2" />
            </div>
        </div>

        <div class="page__row" v-if="mobile">
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
                <div class="products__zero" v-if="items && items.length === 0">
                    <div class="products__title1">Загрузи свой первый трек</div>
                    <div class="products__title2">Твои будущие фанаты ждут! Жми на кнопку «добавить трек» и переходи к продвижению прямо сейчас.</div>
                    <div class="d-flex align-items-center add_content" @click="openMusicModal">
                        <div class="products__add new"></div>
                        <div class="add__music">
                            <div class="color-white title">Добавить трек</div>
                        </div>
                    </div>
                </div>

                <div class="products__grid" v-if="items && items.length > 0">
                    <div class="products__item" @click="openMusicModal" v-if="!mobile">
                        <div class="products__preview new"></div>
                        <div class="products__details">
                            <div class="products__title title">Добавить трек</div>
                        </div>
                    </div>

                    <div class="products__item" v-for="item in items" :key="item.id">
                        <div class="products__preview img">
                            <svg width="18" height="18" viewBox="0 0 14 14" fill="none" xmlns="http://www.w3.org/2000/svg">
                                <circle class="color" cx="7" cy="7" r="6.5" fill="#FF4242"/>
                            </svg>
                            <img v-bind:src="item.image" /> 
                        </div>
                        <div class="products__details">
                            <div class="products__details album color-gray">{{ item.album }}</div>
                            <div class="products__details author color-gray">{{ item.authorName }}</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <b-modal id="music-modal" centered hide-footer>
            <div class="modal-center d-flex flex-column text-center mx-auto">
                <div class="form-block">
                    <input type="text" class="form-control" placeholder="Ссылка на звук в TikTok" v-model="form.url" required="" />
                    <p class="form-tip text-danger" v-if="error" v-html="error" />
                    <button class="btn btn-lg btn-primary btn-block my-4" @click="getMusic(form.url)" :disabled="!form.url" v-if="!waiting" v-html="form.url ? 'Найти трек' : 'Введите ссылку на трек'" />
                    <div class="loading" :class="{active: waiting}" />
                    <p class="form-tip" v-if="waiting" v-html="'Ищем трек, это займет от 5 до 10 секунд'" />
                </div>
            </div>
        </b-modal>

        <b-modal id="music-modal-add" centered hide-footer>
            <div class="modal-center d-flex flex-column text-center mx-auto">
                <div class="form-block">
                    <img class="modal__img" v-bind:src="form.coverThumb" />
                </div>

                <div class="form-block text-left">
                    <label for="music_ref">Ссылка на звук в TikTok</label>
                    <input type="text" class="form-control" placeholder="Ссылка на звук в TikTok" v-model="form.url" required="" id="music_ref" />
                </div>

                <div class="form-block text-left">
                    <label for="music_title">Название</label>
                    <input type="text" class="form-control" placeholder="Название" v-model="form.title" required="" id="music_title" />
                </div>

                <div class="form-block text-left">
                    <label for="music_author">Исполнитель</label>
                    <input type="text" class="form-control" placeholder="Исполнитель" v-model="form.authorName" required="" id="music_author" />
                </div>

                <div class="form-block text-left">
                    <label for="music_album">Альбом</label>
                    <input type="text" class="form-control" placeholder="Альбом" v-model="form.album" required="" id="music_album" />
                </div>

                <div class="form-block modal__add">
                    <button class="btn btn-lg btn-primary btn-block my-4" :disabled="!form.url" @click="addMusic(form)" v-if="!waiting">
                        Добавить
                    </button>
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
                error: null,
                waiting: false,
                items: null,
                form: {
                    url: null,
                    coverThumb: null,
                    title: null,
                    authorName: null,
                    album: null
                }
            }
        },

        mounted() {
            this.getMusicList();
        },

        computed: {
            ...mapGetters(['user', 'userAuthorized', 'url', 'tablet', 'mobile']),
        },

        methods: {
            addMusic({ref, title, authorName, album, url}) {
                axios.post(ADD_MUSIC, {url, title, author: authorName, album})
                .then(response => {
                    this.$bvModal.hide('music-modal-add');
                    this.getMusicList();
                })
                .catch(err => console.warn(err));
            },

            openMusicModal() {
                this.waiting = this.error = false;
                this.$bvModal.show('music-modal');
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
                    let {coverThumb, title, authorName, album} = response.data.music;
                    this.form = {...this.form, coverThumb, title, authorName, album};

                    this.waiting = false;
                    this.error = null;

                    this.$bvModal.hide('music-modal')
                    this.$bvModal.show('music-modal-add');
                }).catch(error => {
                    console.log(error);
                    this.waiting = false;
                    if(error !== undefined) {
                        this.error = 'Не удалось найти трек, попробуйте еще раз';
                    }
                });
            }
        }

    }
</script>
