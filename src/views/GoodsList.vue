<template>
    <div>
        <NavHeader></NavHeader>
        <NavBread>
            <span>Goods</span>
        </NavBread>
        <div class="accessory-result-page accessory-page">
            <div class="container">
                <div class="filter-nav">
                <span class="sortby">Sort by:</span>
                <a href="javascript:void(0)" class="default cur">Default</a>
                <a @click="sortGoods" href="javascript:void(0)" class="price">Price <svg class="icon icon-arrow-short"><use xlink:href="#icon-arrow-short"></use></svg></a>
                <a href="javascript:void(0)" class="filterby stopPop" @click="showFilterPop">Filter by</a>
                </div>
                <div class="accessory-result">
                    <!-- filter -->
                    <div class="filter stopPop" id="filter" v-bind:class="{'filterby-show':filterBy}">
                        <dl class="filter-price">
                        <dt>Price:</dt>
                        <dd><a href="javascript:void(0)" v-bind:class="{'cur':priceChecked=='all'}" @click="priceChecked='all'">All</a></dd>
                        <dd v-for="(item,index) in priceFilter" :key="index">
                            <a href="javascript:void(0)" @click="setPriceFilter(index)" v-bind:class="{'cur':priceChecked==index}">{{item.startPrice}} - {{item.endPrice}}</a>
                        </dd>
                        </dl>
                    </div>
                    <!-- search result accessories list -->
                    <div class="accessory-list-wrap">
                        <div class="accessory-list col-4">
                          <ul>
                              <li class="item" v-for="(item,index) in goodsList" :key="index">
                                <div class="pic">
                                  <a href="#"><img v-lazy="'/static/img/'+item.productImage" alt=""></a>
                                </div>
                                <div class="main">
                                  <div class="name">{{item.productName}}</div>
                                  <div class="price">￥{{item.salePrice}}元</div>
                                  <div class="btn-area">
                                    <a href="javascript:;" class="btn btn--m">加入购物车</a>
                                  </div>
                                </div>
                              </li>
                          </ul>
                          <div class="load-more" v-infinite-scroll="loadMore" infinite-scroll-disabled="busy" infinite-scroll-distance="30">正在玩命加载中...</div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="md-overlay" v-show="overLayFlag" @click="closePop"></div>
        <NavFooter></NavFooter>
    </div>
</template>
<script>
import "./../assets/css/base.css";
import "./../assets/css/product.css";
import "./../assets/css/checkout.css";
import NavHeader from "@/components/Header.vue";
import NavFooter from "@/components/Footer.vue";
import NavBread from "@/components/Bread.vue";
import axios from "axios";
export default {
  data() {
    return {
      priceFilter: [
        {
          startPrice: "0.00",
          endPrice: "500.00"
        },
        {
          startPrice: "500.00",
          endPrice: "1000.00"
        },
        {
          startPrice: "1000.00",
          endPrice: "2000.00"
        }
      ],
      goodsList: [],
      priceChecked: "all",
      filterBy: false,
      overLayFlag: false,
      sortFlag:true,
      page:1,
      pageSize:8,
      busy:true
    };
  },
  mounted() {
    this.getGoodsList()
  },
  components: {
    NavHeader,
    NavFooter,
    NavBread
  },
  methods: {
    showFilterPop() {
      this.filterBy = true;
      this.overLayFlag = true;
    },
    setPriceFilter(index) {
      this.closePop();
      this.priceChecked = index;
    },
    closePop() {
      this.filterBy = false;
      this.overLayFlag = false;
    },
    getGoodsList(flag){
      let param={
        page:this.page,
        pageSize:this.pageSize,
        sort:this.sortFlag?1:-1
      }
      axios.get("/goods",{params:param}).then(res => {
        let msg=res.data
        console.log(res);
        
        if(msg.atatus=="0"){
          if(flag){
            this.goodsList=this.goodsList.concat(msg.result.list)
            if(msg.result.count==0){
              this.busy=true
            }else{
              this.busy=false
            }
          }else{
            this.goodsList=msg.result.list
            this.busy=false
          }
        }else{
          this.goodsList=[]
        }
      });
    },
    sortGoods(){
      this.sortFlag=!this.sortFlag
      this.page=1
      this.getGoodsList()
    },
    loadMore(){
      this.busy=true
      setTimeout(()=>{
        this.page++
        this.getGoodsList(true)
      },500)
    }
  }
};
</script>
<style>
.load-more{
  height: 100px;
  line-height: 100px;
  text-align: center;
}
</style>
