<template>
    <div class="indexlisthot" v-if="hotGoods.length">
        <div class="indexlistheader">
            <img src="../../static/image/hot.png"/>
            <h3>店铺热卖</h3>
        </div>
		<yd-infinitescroll :callback="loadMore" :distance="2" :scroll-top="false" ref="infinitescrollDemo" class="itemgoodslist">
        <yd-list theme="2" slot="list">
            <yd-list-item v-for="(item, key) in hotGoods" :key="key" type="link" :href="{path:'goodsdetail',query:{goods_id:item.id}}">
                <img class="goods-imgs" slot="img" v-lazy="item.image_url">
                <span slot="title">{{item.name}}</span>
                <yd-list-other slot="other">
                    <div>
                        <span class='demo-list-price'><em>¥</em>{{item.price}}</span>
                        <!--<span class='demo-list-del-price'>¥{{item.w_price}}</span>-->
                    </div>
                    <div><button class="indexlist-cart"><img src="../../static/image/cart.png"/></button></div>
                </yd-list-other>
            </yd-list-item>
        </yd-list>
		</yd-infinitescroll>
    </div>
</template>

<script type="text/babel">
export default {
    data () {
        return {
            hotGoods: [],
            page: 1,
            pageSize: 10, // 每页10条数据
        }
    },
    created () {
        /* let where = {hot: 1}
        this.$api.goodsList({where: JSON.stringify(where)}, res => {
            this.hotGoods = res.data.list
        }) */
		this.goodsList()
    },
	methods:{
		goodsList () {
            this.$api.goodsList(this.conditions(),res => {
                const _list = res.data.list
                this.hotGoods = [..._list]
                if (_list.length < this.pageSize) {
                    /* 所有数据加载完毕 */
                    this.$refs.infinitescrollDemo.$emit('ydui.infinitescroll.loadedDone')
                }
                this.page++
            })
        },
        // 加载更多
        loadMore () {
            this.$api.goodsList(this.conditions(),res => {
                const _list = res.data.list
                this.hotGoods = [...this.hotGoods, ..._list]
                if (_list.length < this.pageSize) {
                    /* 所有数据加载完毕 */
                    this.$refs.infinitescrollDemo.$emit('ydui.infinitescroll.loadedDone')
                }
                /* 单次请求数据完毕 */
                this.$refs.infinitescrollDemo.$emit('ydui.infinitescroll.finishLoad')
                this.page++
            })
        },
		conditions () {
		    let where = {hot: 1}
		    let data = {
		        page: this.page,
		        limit: this.pageSize,
		        where: JSON.stringify(where)
		    }
		    return data
		}
	}
}
</script>
