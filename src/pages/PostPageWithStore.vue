<template>
	<div>
		<h1>Страница с постами</h1>
		<my-input
			v-focus
			:model-value="searchQuery"
			@update:model-value="setSearchQuery"
			placeholder="Поиск...."
		>
		</my-input>
		<div class="app__btns">
			<my-button @click="showDialog"> Создать пост</my-button>
			<my-select
				:model-value="selectedSort"
				@update:model-value="setSelectedSort"
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
	</div>
</template>

<script>
import MyButton from '@/components/UI/MyButton.vue';
import MyDialog from '@/components/UI/MyDialog.vue';
import PostForm from '@/components/PostForm';
import PostList from '@/components/PostList';
import MySelect from '@/components/UI/MySelect';
import axios from 'axios';
import { mapState, mapGetters, mapActions, mapMutations } from 'vuex';
export default {
	components: {
		PostForm,
		PostList,
		MyDialog,
		MySelect,
		MyButton,
	},
	data() {
		return {
			dialogVisible: false,
		};
	},
	methods: {
		...mapMutations({
			setPage: 'post/setPage',
			setSearchQuery: 'post/setSearchQuery',
			setSelectedSort: 'post/setSelectedSort',
		}),
		...mapActions({
			loadMorePosts: 'post/loadMorePosts',
			fetchPosts: 'post/fetchPosts',
		}),
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
	},

	mounted() {
		this.fetchPosts();
		// console.log(this.$refs.observer);
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
		...mapState({
			posts: (state) => state.post.posts,
			isPostsLoading: (state) => state.post.isPostsLoading,
			selectedSort: (state) => state.post.selectedSort,
			searchQuery: (state) => state.post.searchQuery,
			page: (state) => state.post.page,
			limit: (state) => state.post.limit,
			totalPages: (state) => state.post.totalPages,
			sortOptions: (state) => state.post.sortOptions,
		}),
		...mapGetters({
			sortedPosts: 'post/sortedPosts',
			sortedAndSearchedPosts: 'post/sortedAndSearchedPosts',
		}),
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
