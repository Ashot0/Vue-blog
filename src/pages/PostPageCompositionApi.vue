<template>
	<div>
		<h1>Страница с постами</h1>
		<my-input v-focus v-model="searchQuery" placeholder="Поиск....">
		</my-input>
		<div class="app__btns">
			<my-button> Создать пост</my-button>
			<my-select
				v-model="selectedSort"
				:options="sortOptions"
			></my-select>
		</div>
		<my-dialog v-model:show="dialogVisible">
			<post-form />
		</my-dialog>

		<post-list :posts="sortedAndSearchedPosts" v-if="!isPostsLoading" />
		<div v-else>Идёт загрузка ...</div>
	</div>
</template>

<script>
import MyDialog from '@/components/UI/MyDialog.vue';
import PostForm from '@/components/PostForm';
import PostList from '@/components/PostList';
import MySelect from '@/components/UI/MySelect';
import axios from 'axios';
import { ref } from 'vue';
import { usePosts } from '@/hooks/usePosts';
import useSortedPosts from '@/hooks/useSortedPosts';
import useSortedAndSearchedPosts from '@/hooks/useSortedAndSearchedPosts';

export default {
	components: {
		PostForm,
		PostList,
		MyDialog,
		MySelect,
	},
	data() {
		return {
			dialogVisible: false,
			sortOptions: [
				{ value: 'title', name: 'По названию' },
				{ value: 'body', name: 'По содержимому' },
				{ value: 'post.id', name: 'По id' },
			],
		};
	},
	setup(props) {
		const { posts, totalPages, isPostsLoading } = usePosts(10);
		const { sortedPosts, selectedSort } = useSortedPosts(posts);
		const { searchQuery, SortedAndSearchedPosts } =
			useSortedAndSearchedPosts(sortedPosts);

		return {
			posts,
			totalPages,
			isPostsLoading,
			sortedPosts,
			selectedSort,
			searchQuery,
			SortedAndSearchedPosts,
		};
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
