<template>
  <div>
    <HeadNav></HeadNav>
    <div class="index-container" id="index-container">
        <div class="menudiv2">
          <ul>
            <li>
              <router-link :to="{name:'detail',params:{type:type,text:title}}">
                <span class="on">
                  首页
                </span>
              </router-link>
            </li>
            <li>
          <router-link :to="{name:'record',params:{type:type,text:title}}">
                <span class="">
                  记录
                </span>
              </router-link>
            </li>
            <li>
              <router-link :to="{name:'model',params:{title:title}}">
                <span class="">
                  模式
                </span>
              </router-link>
            </li>
            <li>
              <router-link :to="{name:'helper',params:{title:title}}">
                <span class="">
                  规则
                </span>
              </router-link>
            </li>
            <li>
              <router-link :to="{name:'statics',params:{title:title}}">
                <span class="">
                  统计
                </span>
              </router-link>
            </li>
            <li>
              <router-link :to="{name:'trend',params:{title:title}}">
                <span class="">
                  走势
                </span>
              </router-link>
            </li>
          </ul>
        </div>
        <div class="moddiv">
            <div class="content">
                <div class="menu">
                    <ul>
                        <li style="width: 100%">
                            <div>
                                <button class="tzms" @click="useModelEvent(2)">单</button>
                                <button class="tzms" @click="useModelEvent(1)">双</button>
                                <button class="tzms" @click="useModelEvent(4)">大</button>
                                <button class="tzms" @click="useModelEvent(3)">小</button>
                                <button class="tzms" @click="useModelEvent(5)">中</button>
                                <button class="tzms" @click="useModelEvent(6)">边</button>
                            </div>
                            <div>
                            </div>
                        </li>
                        <li style="width: 100%">
                            <div>
                                <button class="tzms" @click="betDoubleEvent(0.5)">0.5倍</button>
                                <button class="tzms" @click="betDoubleEvent(1.5)">1.5倍</button>
                                <button class="tzms" @click="betDoubleEvent(2)">2倍</button>
                                <button class="tzms" @click="betDoubleEvent(5)">5倍</button>
                                <button class="tzms" @click="betDoubleEvent(10)">10倍</button>
                                <button class="tzms" @click="betDoubleEvent(100)">100倍</button>
                            </div>
                        </li>
                        <li style="width: 100%">
                            <div>
                                <button class="tzms ora" @click="useModelEvent(0)">全包</button>
                                <button class="tzms ora" @click="useSuohaEvent()">梭哈</button>
                                <button class="tzms ora" @click="unSelectEvent()">反选</button>
                                <button class="tzms ora" @click="initSelectEvent()">清除</button>
                                <button class="tzms ora" @click="getNearTermEvent('1006',813909)">上期</button>
                                <button class="tzms morebtn" @click="popupVisible=true">模式</button>
                            </div>
                        </li>
                        <li style="width: 100%">
                            <div>
                                <button class="chips" @click="useSuohaQuoTaEvent(10000)">10</button>
                                <button class="chips" @click="useSuohaQuoTaEvent(100000)">100</button>
                                <button class="chips" @click="useSuohaQuoTaEvent(300000)">300</button>
                                <button class="chips" @click="useSuohaQuoTaEvent(1000000)">1000</button>
                                <div class="self">
                                    <div class="self1">
                                        <span v-html="fixedQuota"></span>
                                    </div>
                                    <div class="self2">
                                        <button>&nbsp;定额梭哈&nbsp;</button>
                                    </div>
                                </div>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </div>

        <div class="betscontentdiv">
            <table class="bordered">
                <tbody>
                    <tr id="ball_0">
                        <th width="17%">号码</th>
                        <th width="28%" style="color: #eaff01;" colspan="2">标准赔率</th>
                        <th width="30%">投注</th>
                        <th width="25%">倍数</th>
                    </tr>
                    <tr v-for="(rate,index) in rateList">
                        <td align="center" style="border-left: 1px solid #ccc"><span class="num_1">{{rate.Num}}</span></td>
                        <td>{{rate.Odds}}</td>
                        <td><input type="checkbox" @click="chgRateCheckEvent(index)" class="chkRate" v-model="checkboxArray[index]"/></td>
                        <td><input type="number" @blur="chgRateInputEvent(index)" v-model="inputArray[index]"/>
                        </td>
                        <td class="bs">
                            <button @click="butRateEvent(index,0.5)" class="multiple">.5</button>
                            <button @click="butRateEvent(index,2)" class="multiple">2</button>
                            <button @click="butRateEvent(index,10)" class="multiple">10</button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
    <mt-tabbar>
      <div class="content tzl">
          <p class="mytitle">
            期号:<span class="red">{{termno}}</span> 
            金币:<span class="red">{{Thousands(userInfo.amount)}}</span> 
            投注:<span class="red" v-html="Thousands(betTotalAmount)"></span>
          </p>
          <ul class="mytitle">
            <li id="liTimer">第<i>814033</i>期 已开奖，请刷新!</li>
          </ul>
      </div>
      <div class="tzr">
          <a @click="submitBet" class="tzb">确认投注</a>     
      </div>           
    </mt-tabbar>
    <mt-popup v-model="popupVisible" position="right" class="mint-popup">
        <mt-button @click.native="popupVisible = false" size="small" type="danger">关闭</mt-button>
    </mt-popup>
  </div>
</template>
<script>
import { mapMutations,mapState } from 'vuex'
import HeadNav from '../../components/topNav'
import{Toast,MessageBox,Popup} from 'mint-ui'
import{HTTP_URL_API} from '../../data/api'
import {DEFAULT_BET_NUMBER} from '../../data'
import {httpPost,createSign,toThousands} from '../../data/util'
export default{
data(){
    return {
        title:this.$route.params['text'],
        type:this.$route.params['type'],
        code:this.$route.params['code'],
        termno:this.$route.params['termno'],
        rateList:[],
        betTotalAmount:0,//投注总额
        checkboxArray:[],
        inputArray:[],
        fixedQuota:'',
        defaultBetNumber:[],
        maxBetAmount:100000000,//最大投注额度(此处写死,生产环境请读取系统配置)
        minBetAmount:100,//最小投注额度(此处写死,生产环境请读取系统配置)
        popupVisible:false
    }
},    
created () {
    this.setTitle()
  },
mounted(){
	this.getGameRate()			
},  
computed: mapState({
    userInfo: state => state.userInfo
  }), 
methods:{
    ...mapMutations(['CHANGE_TITLE','SHOW_BACK_BUT','USER_CHANGE']),
    setTitle(){
        this.CHANGE_TITLE(this.title)
        this.SHOW_BACK_BUT(true)
    },
    initSelectEvent(){
        let tmpChkArray=[],tmpIptArray=[]
        this.checkboxArray.forEach((obj)=>{
            tmpChkArray.push(false)
            tmpIptArray.push('')
        })
        this.checkboxArray=tmpChkArray
        this.inputArray=tmpIptArray
        this.betTotalAmount=0
    },//清除
    useSuohaEvent(){
        let tmpChkArray=this.checkboxArray,
            tmpIptArray=[],
            tmpBetTotal=0,
            userMaxBetAmount=this.userInfo.amount>this.maxBetAmount?this.maxBetAmount:this.userInfo.amount
        this.inputArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        tmpChkArray.forEach((value,index)=>{
            if(value){
                tmpIptArray.push(Math.floor(userMaxBetAmount / tmpBetTotal * parseInt(this.inputArray[index]))+'')
            }
            else{
                tmpIptArray.push('')
            }
        })
        tmpBetTotal=0
        tmpIptArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
    },//不超最大投注额梭哈
    useModelEvent(__model){
        let tmpChkArray=[],
            tmpIptArray=[],
            tmpBetTotal=0,
            defaultBetAmount=DEFAULT_BET_NUMBER[this.code].split(',')
        switch(__model){
            case 0://全包
                this.defaultBetNumber.forEach((vaule,index)=>{
                    tmpChkArray.push(true)
                    tmpIptArray.push(defaultBetAmount[index])
                    tmpBetTotal+=parseInt(defaultBetAmount[index])
                })                
            break;
            case 1://双
                this.defaultBetNumber.forEach((vaule,index)=>{
                    if(parseInt(vaule[1])%2==0){
                        tmpChkArray.push(true)
                        tmpIptArray.push(defaultBetAmount[index])
                        tmpBetTotal+=parseInt(defaultBetAmount[index])
                    }
                    else{
                        tmpChkArray.push(false)
                        tmpIptArray.push('')
                    }
                })
            break;
            case 2://单
                this.defaultBetNumber.forEach((vaule,index)=>{
                    if(parseInt(vaule[1])%2==1){
                        tmpChkArray.push(true)
                        tmpIptArray.push(defaultBetAmount[index])
                        tmpBetTotal+=parseInt(defaultBetAmount[index])
                    }
                    else{
                        tmpChkArray.push(false)
                        tmpIptArray.push('')
                    }
                })
            break;
            case 3://小
                this.defaultBetNumber.forEach((vaule,index)=>{
                    let number=this.defaultBetNumber.length/2
                    if(index<number){
                        tmpChkArray.push(true)
                        tmpIptArray.push(defaultBetAmount[index])
                        tmpBetTotal+=parseInt(defaultBetAmount[index])
                    }
                    else{
                        tmpChkArray.push(false)
                        tmpIptArray.push('')
                    }
                })
            break;        
            case 4://大
                this.defaultBetNumber.forEach((vaule,index)=>{
                    let number=this.defaultBetNumber.length/2
                    if(index>=number){
                        tmpChkArray.push(true)
                        tmpIptArray.push(defaultBetAmount[index])
                        tmpBetTotal+=parseInt(defaultBetAmount[index])
                    }
                    else{
                        tmpChkArray.push(false)
                        tmpIptArray.push('')
                    }
                })
            break;
            case 5://中
                this.defaultBetNumber.forEach((vaule,index)=>{
                    let number=this.defaultBetNumber.length/3
                    if(index >= number & index < 2 * number - 1){
                        tmpChkArray.push(true)
                        tmpIptArray.push(defaultBetAmount[index])
                        tmpBetTotal+=parseInt(defaultBetAmount[index])
                    }
                    else{
                        tmpChkArray.push(false)
                        tmpIptArray.push('')
                    }
                })            
            break;
            case 6://边
                this.defaultBetNumber.forEach((vaule,index)=>{
                    let number=this.defaultBetNumber.length/4
                    if(index < number + 3 || index > 3 * number - 4){
                        tmpChkArray.push(true)
                        tmpIptArray.push(defaultBetAmount[index])
                        tmpBetTotal+=parseInt(defaultBetAmount[index])
                    }
                    else{
                        tmpChkArray.push(false)
                        tmpIptArray.push('')
                    }
                })            
            break;
        }
        this.checkboxArray=tmpChkArray
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
    },//按钮模式选择
    butRateEvent(__index,__rate){
        let tmpIptArray=[],tmpBetTotal=0,tmpChkArray=this.checkboxArray
        for(let i=0;i<this.inputArray.length;i++)
        {
            if(i==__index && this.inputArray[i]){
                let tmpNumber=parseInt(this.inputArray[i])*__rate
                if(tmpNumber>1){
                    let plusNumber=Math.floor(tmpNumber)
                    tmpIptArray.push(plusNumber+'')
                }
                else{
                    tmpIptArray.push('')
                    tmpChkArray[i]=false
                }
            }
            else{
                tmpIptArray.push(this.inputArray[i])
            }
        }
        tmpIptArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        this.checkboxArray=tmpChkArray
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
    },//按钮加倍(单个)
    betDoubleEvent(__rate){
        let tmpIptArray=[],tmpBetTotal=0,tmpChkArray=this.checkboxArray
        for(let i=0;i<this.inputArray.length;i++)
        {
            if(this.inputArray[i])
            {
                let tmpNumber=parseInt(this.inputArray[i])*__rate
                if(tmpNumber>1)
                {
                    tmpIptArray.push(Math.floor(tmpNumber)+'')
                    tmpBetTotal+=Math.floor(tmpNumber)
                    if(tmpBetTotal>this.maxBetAmount){
                        Toast('卧槽..大赌伤身少投点')
                        return false
                    }
                }
                else
                {
                    tmpIptArray.push('')
                    tmpChkArray[i]=false
                }
            }
            else
                tmpIptArray.push('')
        }
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
        this.checkboxArray=tmpChkArray
    },//加倍(全部)
    chgRateCheckEvent(__index){
        let tmpIptArray=this.inputArray,tmpBetTotal=0,tmpChkArray=this.checkboxArray,
            defaultBetAmount=DEFAULT_BET_NUMBER[this.code].split(',');
        let curChk=tmpChkArray[__index],curIpt=tmpIptArray[__index];
        tmpChkArray.splice(__index,1,!curChk);
        tmpIptArray.splice(__index,1,!curChk?defaultBetAmount[__index]:'');
        tmpIptArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
        this.checkboxArray=tmpChkArray
    },//手动选择框修改
    chgRateInputEvent(__index){
        let tmpIptArray=this.inputArray,tmpBetTotal=0,tmpChkArray=this.checkboxArray,
            defaultBetAmount=DEFAULT_BET_NUMBER[this.code].split(',');
        let curChk=tmpChkArray[__index],curIpt=tmpIptArray[__index];
        if(curIpt){
            tmpChkArray.splice(__index,1,true)
            tmpIptArray.splice(__index,1,this.inputArray[__index])
        }
        else{
            tmpChkArray.splice(__index,1,false)
            tmpIptArray.splice(__index,1,'')
        }
        tmpIptArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
        this.checkboxArray=tmpChkArray
    },//手动输入框修改
    useSuohaQuoTaEvent(__amount){
        let tmpChkArray=this.checkboxArray,
            tmpIptArray=[],
            tmpBetTotal=0
        this.inputArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        tmpChkArray.forEach((value,index)=>{
            if(value){
                tmpIptArray.push(Math.floor(__amount / tmpBetTotal * parseInt(this.inputArray[index]))+'')
            }
            else{
                tmpIptArray.push('')
            }
        })
        tmpBetTotal=0
        tmpIptArray.forEach((value)=>{
            if(value){
                tmpBetTotal+=parseInt(value)
            }
        })
        this.fixedQuota=__amount
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
    },//定额梭哈(<最大投注额 <用户可用余额)
    unSelectEvent(){
        let tmpIptArray=[],tmpBetTotal=0,tmpChkArray=[],
            defaultBetAmount=DEFAULT_BET_NUMBER[this.code].split(',');
        this.defaultBetNumber.forEach((value,index)=>{
            if(this.checkboxArray[index]){
                tmpChkArray.push(false);
                tmpIptArray.push('');
            }
            else{
                let __amount=defaultBetAmount[index];
                tmpChkArray.push(true);
                tmpIptArray.push(__amount);
                tmpBetTotal+=parseInt(__amount);
            }
        })
        this.inputArray=tmpIptArray
        this.betTotalAmount=tmpBetTotal
        this.checkboxArray=tmpChkArray
    },//反选
    getGameRate(){
        let rate={
            c:this.code,
            lang:'cn',
            ticket:this.userInfo.ticket,
            userid:this.userInfo.userId
        }
        httpPost(HTTP_URL_API.GAME_BETRATE,createSign(rate)).then((res)=>{
            if(res){
                this.rateList=res.data.data
                res.data.data.forEach((vaule,index)=>{
                    this.checkboxArray.push(false)
                    this.inputArray.push('')
                    this.defaultBetNumber.push(new Array(index, vaule.Num))
                })
            }
        })
    },//获取游戏固定赔率
    submitBet(){
        if(this.betTotalAmount<this.minBetAmount || this.betTotalAmount>this.maxBetAmount){
            Toast('穷逼..太小了不让玩')
            return;
        }
        if(this.betTotalAmount>this.userInfo.amount){
            Toast('穷逼..没钱就不要玩了!')
            return;            
        }
        var data = {
            gtype: this.code,
            ticket:this.userInfo.ticket,
            userid:this.userInfo.userId,
            no: this.termno,
            betNo: this.inputArray.join(','),
            total: this.betTotalAmount,
            datatype: "json",
            zone: -8
        }
        MessageBox.confirm('大爷..确定投注: '+this.Thousands(this.betTotalAmount)+' 金币?').then(action => {
            httpPost(HTTP_URL_API.GAME_BETINFO,createSign(data)).then((res)=>{
                if(res){
                    if(res.data.code!=0){
                        Toast(res.data.message)  
                    }
                    else{
                        let users ={
                            userName: this.userInfo.userName,
                            nickName: this.userInfo.nickName,
                            userId: this.userInfo.userId,
                            ticket: this.userInfo.ticket,
                            amount: this.userInfo.amount-this.betTotalAmount,
                            cellPhone:this.userInfo.cellPhone,
                            bankAmount:this.userInfo.bankAmount,
                            email:this.userInfo.email
                        }
                        this.USER_CHANGE(users)
                        var instance= Toast('大爷..投注成功')
                        let tmpIptArray=[],tmpChkArray=[]
                        this.defaultBetNumber.forEach((vaule,index)=>{
                            tmpIptArray.push('')
                            tmpChkArray.push(false)
                        })
                        this.checkboxArray=tmpChkArray
                        this.betTotalAmount=0
                        this.inputArray=tmpIptArray
                        setTimeout(() => {
                            instance.close()
                            this.$router.push({name:'detail',params:{
                                code:this.code,
                                type:this.type,
                                text:this.$route.params['text']
                            }})
                        }, 2000)
                    }
                }
            })
	    })
    },//确认投注
    getNearTermEvent(){

    },//获取上一期投注信息
	Thousands(val){
		return toThousands(val)
	}
  },
  components: {HeadNav}
}
</script>
<style scoped>
.menudiv2{float:left;width:100%;background:#4F1511}
.menudiv2 ul{margin:3% 0 7% 1%;padding:0 0 2% 1%}
.menudiv2 li{float:left;margin-right:1%;background-image:url(../../assets/images/game/btn_topyellow.png);background-position:center;background-repeat:no-repeat;padding:.2rem .3rem;border-radius:5px;line-height:140%;height:140%;margin-bottom:2%;list-style:none}
.menudiv2 li a{color:#4F1511;font-size:.85rem}
.menudiv2 .on{color:#ff0000}
.recorddiv{width:100%;margin:1% 0 0 0;padding:0;float:left;font-size:.75rem;font-family:黑体}
.recorddiv table{width:100%;border-spacing:0}
.recorddiv table th,.recorddiv table th{background:url(../../assets/images/game/thbg.png) repeat-x;height:300%;line-height:300%;text-align:center;color:#333333;border-bottom:1px solid #d1d1d1}
.recorddiv table td,.recorddiv table td{height:200%;line-height:160%;background:#fff none repeat scroll 0 0;color:#000;border-right:1px solid #d1d1d1;border-bottom:1px solid #d1d1d1}
.moddiv{width:99%;float:left;margin:0 0 0 0.5%;padding:0%}
.binfodiv .content,.moddiv .content{background:#FFF;margin-top:0.5%;color:#777777}
.binfodiv .content,.moddiv .content{background:#FFF;margin-top:0.5%;color:#777777}
.moddiv ul{margin:2% 0 1.5% 2%;padding:0 0 5% 0}
.moddiv ul li{float:left;padding-bottom:0.5%;list-style:none}
.moddiv ul li a{cursor:pointer}
.tzms{margin:1% 1.5% 1% 0;border:1px solid #a3a3a3;font-size:.75rem;line-height:1.5rem;height:1.5rem;width:15%;float:left;text-align:center;background-image:-moz-linear-gradient(top,#FDFDFE,#F2F4F6);background-image:-webkit-gradient(linear,left top,left bottom,from(#FDFDFE),to(#F2F4F6));filter:progid:DXImageTransform.Microsoft.gradient(startColorstr=#FDFDFE,endColorstr=#F2F4F6);-ms-filter:"progid:DXImageTransform.Microsoft.gradient(startColorstr=#FDFDFE,endColorstr=#F2F4F6)";-webkit-box-shadow:1px 1px 1px rgba(0,0,0,0.2);-moz-box-shadow:1px 1px 1px rgba(0,0,0,0.2);box-shadow:1px 1px 1px rgba(0,0,0,0.2)}
.chips{margin:1% 1% 1% 0;border:1px solid #ccc;font-size:.75rem;line-height:1.5rem;height:1.5rem;width:12.5%;float:left;text-align:center;background-image:-moz-linear-gradient(top,#FDFDFE,#F2F4F6);background-image:-webkit-gradient(linear,left top,left bottom,from(#FDFDFE),to(#F2F4F6));filter:progid:DXImageTransform.Microsoft.gradient(startColorstr=#FDFDFE,endColorstr=#F2F4F6);-ms-filter:"progid:DXImageTransform.Microsoft.gradient(startColorstr=#FDFDFE,endColorstr=#F2F4F6)";-webkit-box-shadow:1px 1px 1px rgba(0,0,0,0.2);-moz-box-shadow:1px 1px 1px rgba(0,0,0,0.2);box-shadow:1px 1px 1px rgba(0,0,0,0.2)}
.self{margin:1% 0;border:1px solid #ccc;color:#8b5100;float:left;line-height:150%;height:150%;padding:0px;width:43%}
.morebtn{color:#fff;background-image:-webkit-gradient(linear,left top,left bottom,from(#F2605F),to(#D73F3E));filter:progid:DXImageTransform.Microsoft.gradient(startColorstr=#F2605F,endColorstr=#D73F3E);-ms-filter:"progid:DXImageTransform.Microsoft.gradient(startColorstr=#F2605F,endColorstr=#D73F3E)"}
.self1{width:50%;float:left}
.avinput{border:medium none;color:#f00;line-height:150%;height:150%;width:100%;padding-left:1%}
.self2{float:right}
.betscontentdiv{float:left;margin:0 0 0 0.5%;padding:0;width:99%;font-size:.9rem}
.betscontentdiv table th{background:#e93f40;color:#FFF}
.bordered{background:#FFF}
.betscontentdiv table td input{background:#fff none repeat scroll 0 0;border:1px solid #ccc;color:#f00;height:auto;text-align:left;width:80%}
.bs a{background:#eee none repeat scroll 0 0;border:1px solid #d7d7d7;border-radius:1px;color:#555;padding:1%;width:24%;margin-left:2%;line-height:100%;float:left}
.num_1{background:#e64047 none repeat scroll 0 0;border-radius:50%;color:#fff;display:block;font-weight:bold;text-align:center;width:20px;height:20px}
.chkRate{width:1.2rem !important;height:1.2rem !important;}
.mint-tabbar{height:66px !important;background-color:#fff !important;font-size:.75rem;}
.mint-tabbar .content{margin-top:3%;color:#777777}
.mint-tabbar .content .mytitle{margin:0;padding:0.5%}
.red{color:#ff0000}
.mint-tabbar .tzl{width:80%;float:left;text-align: left;}
.mint-tabbar .tzr{width:23%;float:right;margin-top:3%;}
.mint-tabbar .tzr .tzb{float:right;color:#fff;background-color:#F05550;line-height:270%;margin:6% 6% 6% 6%;text-align:center;width:105%;border-radius:2px}
.mint-popup{width:80%;height:75%;background-color:#32373b;opacity:0.95;position: fixed;}
.mint-popup .mint-button{position:absolute;width:90%;top:95%;left:5%;transform:translateY(-50%)}
</style>