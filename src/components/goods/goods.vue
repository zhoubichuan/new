<template>
  <div>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
          <ul>
            <li class="menu-item" v-for="(val,index) in goods"  ref="menuList" :key='index'>
              <span class="text border-1px">
                <span class="ico"></span>{{val.name}}
              </span>
            </li>
          </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul>
              <li class="food-list" v-for="(item,index) in goods" refs="foodList" :key='index'> 
                <h1 class="title">{{item.name}}</h1>
                <ul>
                  <li class="food-item border-1px" v-for="(food,index) in item.foods" :key='index'>
                    <div class="ico">
                      <img :src="food.icon" alt="" width="57" height="57">
                    </div>
                    <div class="content">
                      <h2 class="name">{{food.name}}</h2>
                      <p class="desc">{{food.description}}</p>
                      <div class="extra">
                        <span class="count">月售{{food.sellCount}}份</span>
                        <span>好评率{{food.rating}}%</span>
                      </div>
                      <div class="price">
                        <span class="now">¥{{food.price}}</span>
                        <span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
                      </div>
                      <div class="cartcontrol-wrapper">
                        <cartcontrol :food="food"></cartcontrol>
                      </div>
                    </div>
                  </li>
                </ul>
              </li>
            </ul>
        </div>
        <!-- <shopcart ref="shopcart" :selectFoods="sellectFoods"></shopcart> -->
    </div>
    <!-- <food ref="food" :food='seller.food'></food> -->
  </div>
</template>

<script type="text/ecmascript-6">
import BScroll from "better-scroll";
import food from "../food/food";
// import shopcart from '../shopcart/shopcart';
import cartcontrol from "../cartcontrol/cartcontrol";

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: []
    };
  },
  created() {
    this.$http.get("/api/goods").then(response => {
      response = response.body;
      if (response.errno === 0) {
        this.goods = response.data;
        this.$nextTick(() => {
          this._initScroll();
          // this._calculateH
        });
      }
    });
  },
  methods: {
    // selectMenu(index) {
    //     let foodList = this.$refs.foodList;
    //     let el = foodList[index];
    //     this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
    //       click: true,
    //       probeType: 3
    //     });
    //     this.foodsScroll.scrollToElement(el, 300);
    //   },
    _initScroll() {
      this.meunScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 3
      });
      this.foodsScroll.on("scroll", pos => {
        if (pos.y <= 0) {
          this.scrollY = Math.abs(Math.round(pos.y));
        }
      });
    }
  },
  components: {
    food,
    // shopcart,
    cartcontrol
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import '../../common/stylus/mixin.styl';

.goods {
  display: flex;
  position: absolute;
  top: 174px;
  bottom: 46px;
  width: 100%;
  overflow: hidden;

  .menu-wrapper {
    flex: 0 0 80px;
    width: 80px;
    background: #f3f5f7;

    .menu-item {
      display: table;
      height: 54px;
      width: 56px;
      padding: 0 12px;
      line-height: 14px;

      &.current {
        position: relative;
        z-index: 10;
        margin-top: -1px;
        background: #fff;
        font-weight: 700;

        .text {
          border-none();
        }
      }

      .icon {
        display: inline-block;
        vertical-align: top;
        width: 12px;
        height: 12px;
        margin-right: 2px;
        background-size: 12px 12px;
        background-repeat: no-repeat;

        &.decrease {
          bg-image('decrease_3');
        }

        &.discount {
          bg-image('discount_3');
        }

        &.guarantee {
          bg-image('guarantee_3');
        }

        &.invoice {
          bg-image('invoice_3');
        }

        &.special {
          bg-image('special_3');
        }
      }

      .text {
        display: table-cell;
        width: 56px;
        vertical-align: middle;
        border-1px(rgba(7, 17, 27, 0.1));
        font-size: 12px;
      }
    }
  }

  .foods-wrapper {
    flex: 1;

    .title {
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      border-left: 2px solid #d9dde1;
      font-size: 12px;
      color: rgb(147, 153, 159);
      background: #f3f5f7;
    }

    .food-item {
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      border-1px(rgba(7, 17, 27, 0.1));

      &:last-child {
        border-none();
        margin-bottom: 0;
      }

      .icon {
        flex: 0 0 57px;
        margin-right: 10px;
      }

      .content {
        flex: 1;

        .name {
          margin: 2px 0 8px 0;
          height: 14px;
          line-height: 14px;
          font-size: 14px;
          color: rgb(7, 17, 27);
        }

        .desc, .extra {
          line-height: 10px;
          font-size: 10px;
          color: rgb(147, 153, 159);
        }

        .desc {
          line-height: 12px;
          margin-bottom: 8px;
        }

        .extra {
          .count {
            margin-right: 12px;
          }
        }

        .price {
          font-weight: 700;
          line-height: 24px;

          .now {
            margin-right: 8px;
            font-size: 14px;
            color: rgb(240, 20, 20);
          }

          .old {
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
        }

        .cartcontrol-wrapper {
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
  }
}
</style>

