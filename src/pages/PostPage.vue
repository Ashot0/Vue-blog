<template>
	<div>
		<h1>Страница с постами</h1>
		<my-input v-focus v-model="searchQuery" placeholder="Поиск....">
		</my-input>
		<div class="app__btns">
			<my-button @click="showDialog"> Создать пост</my-button>
			<my-select
				v-model="selectedSort"
				:options="sortOptions"
			></my-select>
		</div>
		<my-dialog v-model:show="dialogVisible">
			<post-form @create="createPost" />
		</my-dialog>

		<post-list
			:posts="sortedAndSearchedPosts"
			@remove="removePost"
			v-if="!isPostsLoading"
		/>
		<div v-else>Идёт загрузка ...</div>
		<div v-intersection="loadMorePosts" class="observer"></div>
		<!-- <div class="page__wrapper">
			<div
				v-for="pageNumber in totalPages"
				:key="pageNumber"
				class="page"
				:class="{ 'current-page': page === pageNumber }"
				@click="changePage(pageNumber)"
			>
				{{ pageNumber }}
			</div>
		</div> -->
	</div>
</template>

<script>
import MyDialog from '@/components/UI/MyDialog.vue';
import PostForm from '@/components/PostForm';
import PostList from '@/components/PostList';
import MySelect from '@/components/UI/MySelect';
import axios from 'axios';

export default {
	components: {
		PostForm,
		PostList,
		MyDialog,
		MySelect,
	},
	data() {
		return {
			posts: [],
			dialogVisible: false,
			modificatorValue: '',
			isPostsLoading: false,
			selectedSort: '',
			searchQuery: '',
			page: 1,
			limit: 10,
			totalPages: 0,
			sortOptions: [
				{ value: 'title', name: 'По названию' },
				{ value: 'body', name: 'По содержимому' },
				{ value: 'post.id', name: 'По id' },
			],
		};
	},
	methods: {
		createPost(post) {
			this.posts.push(post);
			this.dialogVisible = false;
		},
		removePost(post) {
			this.posts = this.posts.filter((p) => p.id !== post.id);
		},
		showDialog() {
			this.dialogVisible = true;
		},
		// changePage(pageNumber) {
		// 	this.page = pageNumber;
		// },
		async fetchPosts() {
			try {
				this.isPostsLoading = true;
				setTimeout(async () => {
					const response = await axios.get(
						'https://jsonplaceholder.typicode.com/posts',
						{
							params: {
								_page: this.page,
								_limit: this.limit,
							},
						}
					);
					this.totalPages = Math.ceil(
						response.headers['x-total-count'] / this.limit
					);
					this.posts = response.data;
					this.isPostsLoading = false;
				}, 1000);
			} catch (e) {
				alert('Ошибка!');
			} finally {
			}
		},
		async loadMorePosts() {
			try {
				this.page += 1;
				setTimeout(async () => {
					const response = await axios.get(
						'https://jsonplaceholder.typicode.com/posts',
						{
							params: {
								_page: this.page,
								_limit: this.limit,
							},
						}
					);
					this.totalPages = Math.ceil(
						response.headers['x-total-count'] / this.limit
					);
					this.posts = [...this.posts, ...response.data];
				}, 1000);
			} catch (e) {
				alert('Ошибка!');
			} finally {
			}
		},
	},

	mounted() {
		this.fetchPosts();
		console.log(this.$refs.observer);
		//// const options = {
		//// 	rootMargin: "0px",
		//// 	threshold: 1.0,
		//// };
		//// const callback = (entries, observer) => {
		//// 	if (entries[0].isIntersecting && this.page < this.totalPages) {
		//// 		this.loadMorePosts();
		//// 	}
		//// };
		//// const observer = new IntersectionObserver(callback, options);
		//// observer.observe(this.$refs.observer);
	},
	computed: {
		sortedPosts() {
			return [...this.posts].sort((post1, post2) =>
				post1[this.selectedSort]?.localeCompare(
					post2[this.selectedSort]
				)
			);
		},
		sortedAndSearchedPosts() {
			return this.sortedPosts.filter((post) =>
				post.title
					.toLowerCase()
					.includes(this.searchQuery.toLowerCase())
			);
		},
	},
	watch: {
		// page() {
		// 	this.fetchPosts();
		// },
	},
};
</script>

<style>
.app__btns {
	display: flex;
	justify-content: space-between;
	margin: 15px 0;
}

.page__wrapper {
	display: flex;
	margin-top: 15px;
}
.page {
	border: 1px solid black;
	padding: 10px;
}
.current-page {
	border: 2px solid teal;
}

.observer {
	height: 30px;
	background: green;
}
</style>

<!-- Single file component -->
