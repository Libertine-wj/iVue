<template>
	<div class="pure-menu pure-menu-horizontal">
	    <ul class="pure-menu-list">
	        <li class="pure-menu-item" v-for="menu in menus"
	        	:key="menu.id" 
	        	:class="[menu.id == activeId ? 'pure-menu-selected' : '']"
	        	@click="switchMenu(menu.id)">
	        	<router-link :to="menu.path" class="pure-menu-link">{{menu.name}}</router-link>
	        </li>
	        <div class="cart-count" v-if="cartCount">{{cartCount}}</div>
	    </ul>
	</div>
</template>

<script>
	export default {
		name: "Menus",
		data: function() {
			return {
				activeId: 1,
				menus: [{
					id: 1,
					name: "VMall",
					path: "/vmall"
				}, {
					id: 2,
					name: "My Cart",
					path: "/my-cart"
				}],
				cartCount: 0 //购物车商品数量
			}
		},
		methods: {
			//切换菜单选择状态
			switchMenu: function(menuId) {
				this.activeId = menuId;
			}
		},
		beforeMount: function() { //生命周期函数，监听"购物车"事件
			var that = this;
			that.vHandler.$on("addToCart", function(productId) {
				that.vHandler.productIds.push(productId);
				that.cartCount = that.vHandler.productIds.length;
			});
			that.vHandler.$on("removeFromCart", function(productId) {
				//删除数组中的指定元素
				for(var i=0;i<that.vHandler.productIds.length;i++) {
					if(that.vHandler.productIds[i] == productId) {
						that.vHandler.productIds.splice(i, 1);
						break;
					}
				}
				that.cartCount = that.vHandler.productIds.length;
			});
		},
		mounted: function() { //生命周期函数，设置当前选择菜单
			var currentPath = window.location.hash;
			var path = currentPath.split("#")[1];
			if(path.length > 1) { //判断不是根路径
				for(var i=0;i<this.menus.length;i++) {
					if(path == this.menus[i].path) {
						this.activeId = this.menus[i].id;
						break;
					}
				}
			}
		}
	}
</script>

<style scoped>
	.pure-menu-list {position:relative;}
	.pure-menu-horizontal {padding:0 10%;background-color:rgba(0,0,0,.8);}
	.pure-menu-link {margin-right:1px;padding:1em;font-size:14px;color:#eee;}
	.pure-menu-selected .pure-menu-link, 
	.pure-menu-selected .pure-menu-link:visited {color:#333;background-color:#ffcc33;}
	.pure-menu-active>.pure-menu-link, 
	.pure-menu-link:focus, 
	.pure-menu-link:hover {color:#333;background-color:#ffcc33;}
	.cart-count {position:absolute;top:0;right:0;width:16px;height:16px;
		line-height:16px;text-align:center;font-size:12px;font-weight:bold;
		color:#fff;background-color:#ec0000;border-radius:50%;}
</style>