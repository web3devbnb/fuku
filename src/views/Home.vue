<template>
    <div class="view-home narrow-container">
        <div class="row">
            <div class="col-8 offset-2 col-10-lg offset-1-lg col-12-sm no-offset-sm">
            <h1 class="serarch-heading">Welcome to <span>ICBICB</span></h1>
                <f-search-box></f-search-box>
            </div>
        </div>

        <div class="row row-2-cols-lg no-collapse equal-height">
            <div class="col">
                <router-link :to="{name: 'blocks'}" class="no-effect">
                    <f-card class="home-block block1" hover>
                    <span class="p1"><img src="/homeicon1.png" alt="" class="not-fluid"></span>
                        <h2 class="h3">{{ $t('view_home.blocks') }} <icon data="@/assets/svg/angle-right.svg" color="#999"></icon></h2>
                        <div class="num">
                            <animated-number
                                :value="chainState.blocks | formatHexToInt"
                                :formatValue="formatNum"
                                :duration="numAnimationDuration"
                            />
                        </div>
                    </f-card>
                </router-link>
            </div>
            <div class="col">
                <router-link :to="{name: 'staking'}" class="no-effect">
                    <f-card class="home-block block2" hover>
                    <span class="p1"><img src="/homeicon2.png" alt="" class="not-fluid"></span>
                        <h2 class="h3">{{ $t('view_home.validators') }} <icon data="@/assets/svg/angle-right.svg" color="#999"></icon></h2>
                        <div class="num">
                            <animated-number
                                :value="chainState.validators | formatHexToInt"
                                :formatValue="formatNum"
                                :duration="numAnimationDuration"
                            />
                        </div>
                    </f-card>
                </router-link>
            </div>
            <div class="col">
                <f-card class="home-block block3">
                <span class="p1"><img src="/homeicon3.png" alt="" class="not-fluid"></span>
                    <h2 class="h3">{{ $t('view_home.accounts') }}</h2>
                    <div class="num">
                        <animated-number
                            :value="chainState.accounts | formatHexToInt"
                            :formatValue="formatNum"
                            :duration="numAnimationDuration"
                        />
                    </div>
                </f-card>
            </div>
            <div class="col">
                <router-link :to="{name: 'transactions'}" class="no-effect">
                    <f-card class="home-block  block4" hover>
                    <span class="p1"><img src="/homeicon4.png" alt="" class="not-fluid"></span>
                        <h2 class="h3">{{ $t('view_home.transactions') }} <icon data="@/assets/svg/angle-right.svg" color="#999"></icon></h2>
                        <div class="num">
                            <animated-number
                                :value="chainState.transactions | formatHexToInt"
                                :formatValue="formatNum"
                                :duration="numAnimationDuration"
                            />
                        </div>
                    </f-card>
                </router-link>
            </div>
        </div>
        <div class="row equal-height mat-5">
            <div class="col">
                <f-card class="half-padding">
                    <center><h2 class="h3 n1">Latest <span>Blocks</span></h2> </center>
                    <div class="home-table-in">
                    <home-block-list :items="blocksData" :hidden-columns="['time', 'fee']" :items-per-page="10" class="home-table" />
                      </div>
                    <router-link :to="{name: 'blocks'}" class="btn small secondary" style="width: 100%;">
                        View all blocks
                    </router-link>
                  
                </f-card>
            </div>
        </div>
                <div class="row equal-height mat-5">
            <div class="col">
                <f-card class="half-padding">
                    <center><h2 class="h3 n1">Latest <span>Transactions</span></h2></center>
                    <div class="home-table-in">
                    <home-transaction-list :items="blocksData" :hidden-columns="['gasUsed']" :items-per-page="5" class="home-table" />
                    </div>
                    <router-link :to="{name: 'transactions'}" class="btn small secondary" style="width: 100%;">
                        View all transactions
                    </router-link>
                    
                </f-card>
            </div>
        </div>
        <div class="row equal-height mat-5 l1 l3">
            <div class="col">
                <f-card class="half-padding">
                             <center><h2 class="h3 n1" id="txv">Daily <span>Transactions</span></h2></center>
                              <div class="home-table-in">
                    <f-listbox v-model="txVolumesResolution" :focus-item-on-focus="true" :data="txVolumesResolutions" labeled-by="txv" horizontal />
                    <transaction-volumes :resolution="txVolumesResolution" />
                                        </div>
                </f-card>
            </div>
        </div>

         <div class="row equal-height mat-5 l2">
            <div class="col-10">
                <f-card class="half-padding">
                <div class="home-table-bottom">
                <div class="row no-collapse align-items-center">
                  
                    <div class="col-8">
                    <center><h3>Unlock Wallet to Access Lottery</h3></center>
                    </div>
                    <div class="col-4">
                    <center><a href="#" class="btn large secondary bt1">Unlock Wallet</a></center>
                    </div>
                    
                                        </div>
                                        </div>
                </f-card>
            </div>
        </div>
    </div>
</template>

<script>
    import FCard from "../components/core/FCard/FCard.vue";
    import FSearchBox from "../components/FSearchBox.vue";
    import gql from 'graphql-tag';
    import HomeBlockList from "@/data-tables/HomeBlockList.vue";
    import HomeTransactionList from "@/data-tables/HomeTransactionList.vue";
    import AnimatedNumber from "animated-number-vue";
    import {pollingMixin} from "@/mixins/polling.js";
    import TransactionVolumes from "@/components/TransactionVolumes.vue";
    import FListbox from "@/components/core/FListbox/FListbox.vue";
    import {formatNumberByLocale} from "@/filters.js";

    export default {
        mixins: [pollingMixin],

        components: {
            FListbox,
            TransactionVolumes,
            HomeTransactionList,
            HomeBlockList,
            FCard,
            FSearchBox,
            AnimatedNumber
        },

        data() {
            return {
                blocksData: [],
                txVolumesResolution: '1m',
                txVolumesResolutions: [
                    {
                        label: '2 Weeks',
                        value: '14d',
                    },
                    {
                        label: '1 Month',
                        value: '1m',
                    },
                    {
                        label: '3 Months',
                        value: '3m',
                    },
                ],
                numAnimationDuration: 750,
                chainState: {
                    blocks: 0,
                    validators: 0,
                    accounts: 0,
                    transactions: 0,
                },
            }
        },

        created() {
            this.updateChainState();
        },

        mounted() {
            this._polling.start(
                'update-net-state',
                () => {
                    this.updateChainState();
                },
                3300
            );
        },

        methods: {
            async updateChainState() {
                this.chainState = {...await this.fetchState()};

            },

            /**
             * @returns {Promise<Object>}
             */
            async fetchState() {
                const data = await this.$apollo.query({
                    query: gql`
                        query State {
                            state {
                                blocks
                                transactions
                                accounts
                                validators
                                sfcLockingEnabled
                                sealedEpoch {
                                    id
                                    totalSupply
                                    baseRewardPerSecond
                                }
                            }
                        }
                    `,
                    fetchPolicy: 'network-only',
                });

                return data.data.state || {};
            },

            formatNum(_num) {
                return formatNumberByLocale(parseInt(_num), 0);
            }
        }
    }
</script>

<style lang="scss"> 
.view-home .home-table-in{background:#eaeaea !important;}
.dark-theme .view-home .home-table-in{background:#171717 !important;}
.f-card:not(.off) {box-shadow:none;}
.f-footer{background:#fff;}
.dark-theme .f-footer{background:none;}

.view-home .home-block h2 {color:#fff}
.view-home .home-block .num {color:#fff}
.f-card {background: none!important;}
.dark-theme .home-table-bottom {background: linear-gradient(to bottom, #7c1313, #0d0d0d);border-radius: 5px; text-align: center;
padding: 25px 0;}
.home-table-bottom {background: linear-gradient(to bottom, #c45a5a, #fff);border-radius: 5px; text-align: center;
padding: 25px 0;}
.home-table-bottom .col-8, .home-table-bottom .col-2 {text-align: center;}
.l2 .col-10 {margin: 0 auto;}
.l2 .col-10 h3 {margin: 0;}
.home-table-bottom  h3 {margin: 0;
font-size: 26px;
text-shadow: 0px 1px 1px #000;}
.bt1 {color:#fff!important; background: #0d0d0d!important; border: none!important;}
.home-table-bottom h3 {text-align: center;color:#fff;}
.f-data-table .mobile-view .mobile-item {background: none!important;}
.f-navigation nav ul li:last-child { box-shadow:none !important; }             
.flistbox-horizontal .flistbox_list {
    flex-direction: row;
    width: max-content;
    border-radius: 25px;
}
.view-home .f-search-box input {
 background:#fafafa;
 box-shadow:none;   
}
.n1 {
text-align: center;
font-weight: normal;
text-transform: uppercase;
border-left: 8px solid #ff1515;
font-size: 26px;
margin-bottom: 35px;
width: max-content;
padding-left: 15px;
}
.n1  { span {color:#ff1515;} }
    .view-home {
        .f-search-box {
            width: 100%;
            padding: 8px 0 40px 0;
            //padding: 64px 0;
        }
        .l1 .home-table-in{ border: 1px solid #ff1515;}
       .home-table-in {background: #171717;
        border-radius: 5px;
        padding: 15px 15px 5px!important;
        margin-bottom: 10px;}

     


       
           .f-search-box {background: linear-gradient(to right, #ff1515, #551210);
                padding: 1px;
                border-radius: 28px;}

        .serarch-heading {text-align: center; margin-bottom: 5px;} 
        .serarch-heading span {color:#ff1515;}
        .f-search-box {margin-bottom: 25px;}
        .f-search-box .svg-icon {
    color: #ff1515!important;
}
 .f-search-box:hover .btn {
    color: #fff!important;
    background: #ff1515!important;
}
 .f-search-box:hover .svg-icon {
    color: #fff!important;
}
.block1 {background: linear-gradient(to right, #541275, #a81f7e)!important;}
.block2 {background: linear-gradient(to right, #2d1976, #3842cb)!important;}
.block3 {background: linear-gradient(to right, #5e6195, #a09dd5)!important;}
.block4 {background: linear-gradient(to right, #8927c9, #551c95)!important;}
.p1, .p2, .p3, .p4 {background: rgba(255, 255, 255, 0.3);
border-radius: 40px;
width: 50px;
height: 50px;
text-align: center;
display: inline-block;
margin: 10px 0 15px;}
.p1 img, .p2 img, .p3 img, .p4 img{
width: 50px;

}

        .home-block {
            --f-card-padding: 30px;
            display: flex;
            flex-direction: column;
            align-items: left;
            align-content: left;
            justify-content: left;
            background: #000;
            min-height: 195px;

            h2 {
                text-align: left;
                //margin-top: 16px;
                margin-bottom: 4px;
            }

            .num {
                text-align: left;
                font-weight: bold;
                font-size: 38px;
            }
        }

        .home-table {
            margin-bottom: 16px;
            height: 300px;
        }

        .txvolumes_label {
            display: flex;
            flex-wrap: wrap;
            //gap: 16px;
            align-items: center;
            margin-bottom: 4px;

            h2 {
                margin-bottom: 0;
                padding-inline-end: 16px;
            }
        }
    }

    @include media-max($bp-menu) {
        .view-home {
            .f-search-box {
                padding: 32px 0;
            }
        }
    }

    @include media-max($bp-small) {
        .view-home {
            .home-block {
                min-height: 136px;

                h2 {
                    font-size: $fs16;
                }

                .num {
                    font-size: 28px;
                }
            }

            .txvolumes_label {
                //font-size: 14px;
                h2 {
                    margin-bottom: 8px;
                }
            }
        }
    }
     .dark-theme .f-search-box input {background: #171717; text-align: left!important; font-size: 14px;}
</style>
