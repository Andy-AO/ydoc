<!-- home 文档库列表 -->
<template>
    <div class="c-home-library-list">
        <!-- 文档库检索 -->
        <el-card class="box-card" shadow="hover">
            <c-home-library-list-bar @search="onLibraryGroupSearch" />
        </el-card>

        <!-- 文档库列表 -->
        <el-scrollbar class="scrollbar">
            <c-infinite-list ref="libraryList" :request="getLibraryList">
                <div class="library-list">
                    <el-card v-for="library in libraryList" :key="library.id" class="box-card" shadow="hover">
                        <div class="library-item" @click="onLibraryContent(library)">
                            <el-image class="library-item__image" :src="library.library_info.cover">
                                <div slot="error" class="image-slot__error">
                                    ydoc
                                </div>
                            </el-image>
                            <div class="library-item__main">
                                <!-- 文档库基本信息 -->
                                <div class="library-item__name text-overflow" :title="library.library_info.name">
                                    {{ library.library_info.name }}
                                </div>
                                <div class="library-item__desc" :title="library.library_info.desc">
                                    {{ library.library_info.desc || '暂无简介' }}
                                </div>

                                <!-- 文档库操作 -->
                                <div class="library-item__actions">
                                    <span class="library-item__actions-item" title="相关分享" @click.stop="onLibraryShare(library)">
                                        <i class="el-icon-share"></i>
                                    </span>
                                    <span class="library-item__actions-item" title="文档库管理"
                                        @click.stop="onLibraryManager(library)">
                                        <i class="el-icon-s-tools"></i>
                                    </span>
                                </div>
                            </div>
                        </div>
                    </el-card>
                </div>

                <div slot="empty-tip" class="bold-normal">
                    <span>暂无文档库，</span>
                    <el-button class="quick-library-create" type="text" @click="onLibraryWillCreate">立即创建</el-button>
                </div>
            </c-infinite-list>
        </el-scrollbar>

        <!-- 文档库分享 -->
        <c-library-drawer-library-share :visible.sync="libraryShare.visible" :library-id="libraryShare.libraryId" />
    </div>
</template>

<script>
    export default {
        name: 'c-home-library-list',
        components: {
            'c-infinite-list': () => import('@/components/c-infinite-list'),
            'c-home-library-list-bar': () => import('@/components/home/c-home-library-list-bar'),
            'c-library-drawer-library-share': () => import('@/components/library/drawer/c-library-drawer-library-share')
        },
        data() {
            return {
                libraryList: [],
                libraryShare: { visible: false, libraryId: 0 }
            };
        },
        methods: {
            // 获取文档库列表
            async getLibraryList(page, handler) {
                let hres = {};
                await this.$api.v1.LibraryList(page).then(({ resData, res }) => {
                    this.$utils.ArrayConcat(this.libraryList, resData.list);
                    hres = res;
                });
                handler(hres);
            },
            // 事件：查看文档库内容
            onLibraryContent(library) {
                this.$link.libraryContent({ library_id: library.library_id });
            },
            // 事件：文档库分组查询
            onLibraryGroupSearch({ search_key, group_id }) {
                this.libraryList = [];
                this.$refs.libraryList && this.$refs.libraryList.initList({ search_key, group_id });
            },
            // 事件：管理文档库
            onLibraryManager(library) {
                this.$link.libraryInfo({ library_id: library.library_id });
            },
            // 事件：文档库相关分享
            onLibraryShare(library) {
                this.libraryShare.libraryId = library.library_id;
                this.libraryShare.visible = true;
            },
            // 事件：文档库将要创建
            onLibraryWillCreate() {
                this.$link.libraryCreate();
            }
        }
    };
</script>

<style lang="scss">
    .c-home-library-list {
        .image-slot__error {
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 20px;
            color: #c0c4cc;
            vertical-align: middle;
            width: 100%;
            height: 100%;
            background: #f5f7fa;
        }

        .quick-library-create {
            font-size: 15px;
            color: $--color-primary-light-3;
            &:hover {
                color: $--color-primary-light-1;
            }
        }
    }
</style>

<style lang="scss" scoped>
    .library-list--many {
        .library-list {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            > .el-card {
                width: 49%;
            }
        }
    }

    .library-item {
        display: flex;

        &__image {
            width: 90px;
            height: 90px;
            border-radius: 7px;
            border: 1px solid #f5f7fa;
        }

        &__main {
            margin-left: 10px;
            flex: 1;
            position: relative;
            cursor: pointer;
            padding: 5px 0px;
        }

        &__name {
            font-size: 16px;
            color: $--color-primary-light-1;
        }

        &__desc {
            line-height: 18px;
            margin: 7px 0 10px 0;
            color: $--color-primary-light-7;
            font-size: 12px;
            text-overflow: -o-ellipsis-lastline;
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            line-clamp: 2;
            -webkit-box-orient: vertical;
            word-break: break-all;
        }

        &__actions {
            margin-top: 10px;
            position: absolute;
            right: 0;
            bottom: 0;
            opacity: 0.5;
            transition: opacity 0.3s;
            display: flex;

            &-item {
                font-size: 13px;
                background: #f2f2f2;
                padding: 4px 6px;
                border-radius: 5px;
                cursor: pointer;
                margin-left: 7px;
            }
        }

        &:hover .library-item__actions {
            opacity: 1;
        }
    }

    .scrollbar {
        height: calc(100vh - 190px);
    }
</style>