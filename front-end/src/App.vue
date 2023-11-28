<template>
	<NavBar :user="user" :products="cartItems" />
	<div class="page-wrap">
		<router-view :user="user"></router-view>
	</div>
</template>

<script>
import { getAuth, onAuthStateChanged } from 'firebase/auth';
import axios from 'axios'
import NavBar from '@/components/NavBar.vue';

export default {
	name: 'App',
	components: {
		NavBar,
	},
	data() {
		return {
			user: null,
			cartItems: [],
		};
	},
	watch: {
		async user(newUserValue) {
			console.log('Changed!')
			console.log(newUserValue)
			if (newUserValue) {
				const cartResponse = await axios.get(
					`/api/users/${newUserValue.uid}/cart`
				)
				const cartItems = cartResponse.data
				this.cartItems = cartItems
			}
		},
	},
	async created() {
		const auth = getAuth();
		onAuthStateChanged(auth, (user) => {
			this.user = user;
		});

		if (this.user) {
			const response = await axios.get(`/api/users/${this.user.uid}/cart`)
			const cartItems = response.data
			this.cartItems = cartItems
		}
	},
};
</script>
