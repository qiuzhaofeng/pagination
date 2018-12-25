<template>
  <el-pagination
    class='el-pagination-box'
    v-show="total > 0"
    @size-change="handleSizeChange"
    @current-change="handleCurrentChange"
    :current-page.sync="curPage"
    :page-sizes="pageSizes"
    :page-size="pageSize"
		:data-id ='id' 
    layout="slot, prev, pager, next, slot, total, jumper, sizes, slot"
    :total="total"
    >
    <h1 class="pageTitle" @click="goFirLas($event)">显示第{{(curPage-1)*pageSize+1}}到第{{((curPage-1)*pageSize+pageSize)>total?total:(curPage-1)*pageSize+pageSize}}条的记录，总共{{total}}条记录</h1>
  </el-pagination>

</template>
<script>

export default {
	props: {
    curr_page: {
      type: Number,
      default: 1
    },
    total: {
      type: Number,
      default: 0
		},
		page_size: {
			type: Number,
      default: 10
		},
		// 针对一个页面有多个分页需传递不同的id
		id: {
			type: String,
			default: 'defaultId'
		},
		pageSizes: {
			type: Array,
      default: function () {
        return [5, 10, 20, 30, 40, 50]
      }
		}
  },
	data () {
		return {
      // pageSizes: [5, 10, 20, 30, 40, 50],
			// 当前页数据操作需要传到父组件，直接操作传过来的curr_page，vue会警告，所以在这里转一下
      pageSize: this.page_size,
			curPage: this.curr_page
		}
	},
	mounted() {
		this.setTotalPage();
	},
  methods: {
    handleSizeChange(val) {
			this.pageSize = val;
			// 给第一页添加状态
			this.curPage = 1
      this.$emit('change', 1, this.pageSize);
    },
    handleCurrentChange(val) {
			this.curPage = val;
      this.$emit('change', this.curPage, this.pageSize);
    },
    // 首尾页事件
    goFirLas(event) {
      var evt = window.event||event;
      var target = evt.srcElement ? evt.srcElement : evt.target
      if(target.offsetLeft==0&&target.offsetHeight>23) {
				// 当前页处于第一页不触发首页事件
				if(!(this.curPage==1)) {
        	this.handleCurrentChange(1)
				}
      } else if (target.offsetLeft>0&&target.offsetHeight>23) {
				// 当前页处于最后一页不触发尾页事件
				if(!(this.curPage==Math.ceil(this.total/this.pageSize))) {
        	this.handleCurrentChange(Math.ceil(this.total/this.pageSize))
				}
      }
		},
		// 总共页数事件
		setTotalPage(){
			var pagination = document.querySelector(`[data-id="${this.id}"]`);
			var total = pagination.getElementsByClassName('el-pagination__total')[0];
			total.innerHTML = `共${Math.ceil(this.total/this.pageSize)}页，`;
			// var pagination = document.getElementsByClassName('el-pagination-box')[0];
      // var total = pagination.getElementsByClassName('el-pagination__total')[0];
      // total.innerHTML = `共${Math.ceil(this.total/this.pageSize)}页，`;
		}
  },
  watch: {
    // 监听总页数，可以获取到请求回来的total，在mounted获取执行一次并且获取的是刚出入的total
    total:function() {
			this.setTotalPage();
		},
		// 当pageSize改变的时候，总共多少页改变，监听pageSize能成功不是因为父组件穿过来的时候改变触发的，而是在组件里面就能触发
		// handleSizeChange方法里this.pageSize = val的改变
		pageSize:function() {
			this.setTotalPage();
		},
		// 点击非第一页，再次请求数据（查询）total不会改变不会重新渲染dom，这里点击查询不会触发事件让this.curPage改变，
		// 状态会停留在非第一页，这里监听（注意要监听传过来的值），监听转的值监听不到，因为父组件传过来值变化，data里面转的不会变化
    curr_page: function(val){
      this.curPage = val;
    }
  }
}
</script>
<style lang="less">
  // 此处不加scoped是为了分页代码覆盖全局，也可以放到全局，目前发到这里，便于修改
  .el-pagination {
			padding-left: 0px;
			position: relative;
			padding-top: 30px !important;
			margin-top: 10px !important;
			margin-bottom: 15px !important;
			.pageTitle {
				margin: 0;
				&:nth-of-type(3) {
					font-size: 12px;
					color: #666;
					font-weight: 500;
					padding-bottom: 10px;
					position: absolute;
    			top: 8px;
				}
				&:nth-of-type(1),&:nth-of-type(2) {
					display: inline-block;
					box-sizing: border-box;
					border: 1px solid #999;
					width: 18px;
					height:28px;
					padding-left: 16px;
					overflow: hidden;
					cursor: pointer;
				}
				&:nth-of-type(1) {
					background:url('./img/first_page.png') no-repeat center;
					&:hover {
						border-color: #27b2fb;
						background:#91d8fd url('./img/first_page_on.png') no-repeat center;
					}
				}
				&:nth-of-type(2) {
					background:url('./img/last_page.png') no-repeat center;
					&:hover {
						border-color: #27b2fb;
						background:#91d8fd url('./img/last_page_on.png') no-repeat center;
					}
					margin-right: 27px;
				}
			}
			button {
				border: 1px solid #999;
				padding: 0px 0px !important;
				width: 18px;
				height: 28px;
				min-width:18px;
				&.btn-prev {
					margin-left: 12px;
					.el-icon {
						font-size: 16px;
						font-weight: 500;
						color: #a7aebb;
					}
					&:hover {
						border-color: #27b2fb;
						background-color: #91d8fd;
						.el-icon {
							color: #27b2fb;
						}
					}
				}
				&.btn-next {
					margin-right: 12px;
					.el-icon {
						font-size: 16px;
						font-weight: 500;
						color: #a7aebb;
					}
					&:hover {
						border-color: #27b2fb;
						background-color: #91d8fd;
						.el-icon {
							color: #27b2fb;
						}
					}
				}
				&:disabled {
					cursor: pointer;
				}
				
			}
			.el-pager {
				li {
					border: 1px solid #999;
					min-width: 38px;
					margin-left: 12px;
					font-size: 14px;
					font-weight: 500;
					font-family: '微软雅黑';
					cursor: pointer;
					color: #999;
					&:last-child {
						margin-right: 12px;
					}
					&:hover {
						&:not(.active){
							color:#999;
						}
					}
					&.active {
						color: #2ca1ce;
						border-color: #27b2fb;
						background-color: #91d8fd;
          }
          &.active + li {
						border-left: 1px solid #999;
					}
				}
			}
			.el-pagination__jump {
				margin-top: -22px;
    		vertical-align: middle !important;
				margin-left: 0;
				margin-right: 10px;
				font-size: 16px !important;
				color: #999;
				.el-pagination__editor.el-input {
					width: 38px;
					padding: 0px;
					margin: 0 5px;
					.el-input__inner {
						border-radius: 0px;
						border: 1px solid #ababab;
						font-size: 16px;
						color: #999;
					}
				}
			}
			.el-pagination__total {
				font-size: 16px !important;
				color: #999;
				margin-right: 0;
			}
			.el-pagination__sizes {
				.el-select {
					.el-input {
						margin: 0;
						width: 82px;
						.el-input__inner {
							border-radius: 0;
							border: 1px solid #ababab;
							font-size: 13px;
							color: #999;
						}
					}
				}
			}
		}
</style>