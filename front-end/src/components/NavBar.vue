<template>
	<div class="nav-bar">
		<router-link to="/products" class="products-link">
			<div class="logo-wrap">
				<img :src="logo" />
			</div>
		</router-link>
		<div class="nav-buttons-wrap">
			<button class="sign-out" @click="signOut" v-if="user">Sign Out</button>
			<router-link to="/cart">
				<button>Shopping Cart ({{ cartCount }})</button>
			</router-link>
		</div>
	</div>
</template>

<script>
import axios from 'axios';
import { getAuth, signOut } from 'firebase/auth';
import logo from '@/assets/logo-hexagon.svg';

export default {
	name: 'NavBar',
	props: ['user'],
	data() {
		return {
			logo,
			cartItems: [],
		};
	},
	computed: {
		cartCount() {
			return Object.keys(this.cartItems).length;
		},
	},
	watch: {
		async user(newUserValue) {
			if (newUserValue) {
				const cartResponse = await axios.get(
					`/api/users/${newUserValue.uid}/cart`
				);
				const cartItems = cartResponse.data;
				this.cartItems = cartItems;
			}
		},
	},
	methods: {
		signOut() {
			const auth = getAuth();
			signOut(auth);
		},
	},
	async created() {
		if (this.user) {
			const response = await axios.get(`/api/users/${this.user.uid}/cart`);
			const cartItems = response.data;
			this.cartItems = cartItems;
		}
	},
};
</script>
