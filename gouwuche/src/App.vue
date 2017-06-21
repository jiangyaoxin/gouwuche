<template>
  <div id="app">
   	<div class="catbox">
		  <table id="cartTable">
		    <thead>
		      <tr>
		        <th><label  v-on:click="allCheck()" v-bind:class="{'opColor':checkFlag}">
		        	<icon name="checkbox" class="check-all check"></icon>&nbsp;&nbsp;全选</label></th>
		        <th>商品</th>
		        <th>单价</th>
		        <th>数量</th>
		        <th>小计</th>
		        <th>操作</th>
		      </tr>
		    </thead>
		    <tbody>
		      <tr v-for="item in proList">
		        <td class="checkbox" v-bind:class="{'opColor':item.checkedP}"  v-on:click="selectProduct(item)"><icon name="checkbox" class="check-one check"></icon></td>
		        <td class="goods"><img v-bind:src="item.src" alt=""/><span>{{item.name}}</span></td>
		        <td class="price">{{item.price}}</td>
		        <td class="count"><span class="reduce" v-on:click="numChange(-1,item)">-</span>
		          <input class="count-input" type="text" v-model="item.num"/>
		          <span class="add" v-on:click="numChange(1,item)">+</span></td>
		        <td class="subtotal">{{item.num*item.price|reverse}}</td>
		        <td class="operation"><span class="delete" v-on:click="deleteOne(item)">删除</span></td>
		      </tr>
		    </tbody>
		  </table>
		  <div class="foot " id="foot" v-bind:class="{'show':isFlag}">
		    <label class="fl select-all" v-bind:class="{'opColor':checkFlag}"  v-on:click="allCheck()"><icon name="checkbox"  class="check-all check"></icon>&nbsp;&nbsp;全选</label>
		    <a class="fl delete" id="deleteAll" href="javascript:;" v-on:click="deleteSel()">删除</a>
		    <div class="fr closing" onclick="getTotal();">结 算</div>
		    <input type="hidden" id="cartTotalPrice" />
		    <div class="fr total">合计：￥<span id="priceTotal">{{addprice|reverse}}</span></div>
		    <div class="fr selected" id="selected">已选商品<span id="selectedTotal">{{totalNum}}</span>件<span class="arrow up" v-on:click="up()">︽</span><span class="arrow down" v-on:click="down()">︾</span></div>
		    <div class="selected-view">
		      <div id="selectedViewList" class="clearfix">
		        <div v-for="(item,index) in checkList"><img v-bind:src="item['msg'].src" alt=""/><span v-on:click="qxxz(item,index)">取消选择</span></div>
		      </div>
		      <span class="arrow">◆<span>◆</span></span> </div>
		  </div>
		</div>
  </div>
</template>
<script type="text/javascript" src="../static/data.js" ></script>
<script>
import Product from '../static/data.json'
import Vue from 'vue'
Vue.filter('reverse', function (value) {
 	return value.toFixed(2)
})
export default {
    name: 'app',
    data(){
    	return {
       	proList:Product["list"],
       	checkFlag:false,
       	addprice:0,
       	checknum:0,
       	totalNum:0,
       	checkList:[],
       	isFlag:false
			}
		},
		created () {
    },
    methods: {
        routeChange (to, from) {
            //这里如何获取comment.vue的data呢？
        },
        numChange:function(num,item){
        	if(num>0){
        	 item.num++;
        	}else if(num<0){
        		if(item.num>1){
        			item.num--;
        		}
        	}
        	this.addPrice();
        	this.ttnum();
        },
        selectProduct:function(item){
        	if(typeof item.checkedP=='undefined'){
        		Vue.set(item,'checkedP','true')
        		this.checknum++;
        	}else{
        		item.checkedP=!item.checkedP;
        	}
        	this.addPrice();
        	this.ttnum();
        },
        allCheck:function(){
        	this.checkFlag=!this.checkFlag;
        	var _this=this;
	    		for(let i=0;i<_this.proList.length;i++){
	    			if(typeof _this.proList[i].checkedP=='undefined'){
		        	Vue.set(_this.proList[i],'checkedP','true')
		        	console.log(_this.proList[i]);
		        }else{
		        	_this.proList[i].checkedP=_this.checkFlag;
		        	console.log(_this.proList[i]);
		        }
	    		}
	    		this.checknum=this.proList.length;
	    		this.addPrice();
	    		this.ttnum();
      	},
       	addPrice:function(){
       		var _this=this;
       		_this.addprice=0;
	    		for(let i=0;i<_this.proList.length;i++){
	    			if (_this.proList[i].checkedP) {
	    				_this.addprice+=_this.proList[i].num*_this.proList[i].price;
	    			}
	    		}
       	},
       	deleteSel:function(){
       		var _this=this;
       		let arr = [];
       		if (this.checknum>0) {
       			var con = confirm('确定删除所选商品吗？'); 
       			if (con) {
	            for (let i=0;i<_this.proList.length;i++) {
	              if (!_this.proList[i].checkedP) {
			    				arr.push(_this.proList[i]);
			    			}
	            }
	            this.proList=arr;
	       			this.addPrice();
	          }      
       		}else {
            alert('请选择商品！');
        	}	
        	this.ttnum();
       	},
       	ttnum:function(){
       		var _this=this;
       		_this.totalNum=0;
       		this.checkList=[];
       		for (let i=0;i<_this.proList.length;i++) {
	          if (_this.proList[i].checkedP) {
		    				_this.totalNum+=Number(_this.proList[i].num);
		    				_this.checkList.push({"id":i,"msg":_this.proList[i]});
		    			}
	        }
       		console.log(this.checkList);
       	},
       	deleteOne:function(item){
       		var _this=this;
       		let a='';
       		var con = confirm('确定删除所选商品吗？'); 
	   			if (con) {
            for (let i=0;i<_this.proList.length;i++){
            	if(_this.proList[i]===item){
            		_this.proList.splice(i,1);
            	}
            }
	        }  
	        this.addPrice();
	    		this.ttnum();
       	},
       	qxxz:function(item,index){
       		this.checkFlag=false;
       		this.proList[item['id']].checkedP=false;
       		this.checkList.splice(index,1);
       		this.addPrice();
	    		this.ttnum();
	    		console.log(this.checkList.length);
	    		if(this.checkList.length==0){
						this.isFlag=false;
					}   
       	},
       	up:function(){
					if(this.checkList.length>0){
						this.isFlag=true;
					}     		
       	},
       	down:function(){
       		this.isFlag=false;
       	}
    }
}
</script>

<style>
table tr td.opColor,.foot .select-all.opColor,table th label.opColor{color: orange;}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
@charset "utf-8";
*{margin:0;padding:0;list-style-type:none;}
a{color:#666;text-decoration:none;}
table{border-collapse:collapse;border-spacing:0;border:0;}
body{color:#666;font:12px/180% Arial, Helvetica, sans-serif, "新宋体";}
clearfix:after{content:".";display:block;height:0;clear:both;visibility:hidden}
.clearfix{display:inline-table}
*html .clearfix{height:1%}
.clearfix{display:block}
*+html .clearfix{min-height:1%}
.fl{float:left;}
.fr{float:right;}
/*素材家园 - www.sucaijiayuan.com*/
.catbox{width:940px;margin:100px auto;}
.catbox table{text-align:center;width:100%;}
.catbox table th,.catbox table td{border:1px solid #CADEFF;}
.catbox table th{background:#e2f2ff;border-top:3px solid #a7cbff;height:30px;}
.catbox table td{padding:10px;color:#444;}
.catbox table tbody tr:hover{background:RGB(238,246,255);}
.checkbox{width:60px;}
.check-all{ vertical-align:middle;}
.goods{width:300px;}
.goods span{width:180px;margin-top:20px;text-align:left;float:left;}
.goods img{width:100px;height:80px;margin-right:10px;float:left;}
.price{width:130px;}
.count{width:90px;}
.count .add, .count input, .count .reduce{float:left;margin-right:-1px;position:relative;z-index:0;}
.count .add, .count .reduce{height:23px;width:17px;border:1px solid #e5e5e5;background:#f0f0f0;text-align:center;line-height:23px;color:#444;}
.count .add:hover, .count .reduce:hover{color:#f50;z-index:3;border-color:#f60;cursor:pointer;}
.count input{width:50px;height:15px;line-height:15px;border:1px solid #aaa;color:#343434;text-align:center;padding:4px 0;background-color:#fff;z-index:2;}
.subtotal{width:150px;color:red;font-weight:bold;}
.operation span:hover,a:hover{cursor:pointer;color:red;text-decoration:underline;}

.foot{margin-top:0px;color:#666;height:48px;border:1px solid #c8c8c8;border-top:0;background-color:#eaeaea;background-image:linear-gradient(RGB(241,241,241),RGB(226,226,226));position:relative;z-index:8;}
.foot div, .foot a{line-height:48px;height:48px;}
.foot .select-all{width:80px;height:48px;line-height:48px;color:#666;text-align:center;}
.foot .delete{padding-left:10px;}
.foot .closing{border-left:1px solid #c8c8c8;width:103px;text-align:center;color:#666;font-weight:bold;cursor:pointer;background-image:linear-gradient(RGB(241,241,241),RGB(226,226,226));}
.foot .closing:hover{background-image:linear-gradient(RGB(226,226,226),RGB(241,241,241));color:#333;}
.foot .total{margin:0 20px;cursor:pointer;}
.foot  #priceTotal, .foot #selectedTotal{color:red;font-family:"Microsoft Yahei";font-weight:bold;}
.foot .selected{cursor:pointer;}
.foot .selected .arrow{position:relative;top:-3px;margin-left:3px;}
.foot .selected .down{position:relative;top:3px;display:none;}
.show .selected .down{display:inline;}
.show .selected .up{display:none;}
.foot .selected:hover .arrow{color:red;}
.foot .selected-view{width:938px;border:1px solid #c8c8c8;position:absolute;height:auto;background:#ffffff;z-index:9;bottom:48px;left:-1px;display:none;}
.show .selected-view{display:block;}
.foot .selected-view div{height:auto;}
.foot .selected-view .arrow{font-size:16px;line-height:100%;color:#c8c8c8;position:absolute;right:330px;bottom:-9px;}
.foot .selected-view .arrow span{color:#ffffff;position:absolute;left:0px;bottom:1px;}

#selectedViewList{padding:10px 20px 10px 20px;}
#selectedViewList div{display:inline-block;position:relative;width:100px;height:80px;border:1px solid #ccc;margin:10px;float:left;}
#selectedViewList div img{width:100px;height:80px;margin-right:10px;float:left;}
#selectedViewList div span{display:none;color:#ffffff;font-size:12px;position:absolute;top:0px;right:0px;width:60px;height:18px;line-height:18px;text-align:center;background:#000;cursor:pointer;}
#selectedViewList div:hover span{display:block;}
</style>
