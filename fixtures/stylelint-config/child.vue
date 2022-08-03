<template>
  <div class="child-box" ref="childBox">
    <div class="child-box-child">
      <div class="child-box-child-user">
        <div
          class="child-box-child-user-icon"
          ref="childBorder"
          @click="jumpLinkClick"
        >
          <div class="child-box-child-user-icon-icon">
            <img :src="childIcon" />
          </div>
        </div>
        <div class="child-box-child-user-title" ref="childTitle">
          <div class="child-box-child-user-title-text">{{ childTitle }}</div>
        </div>
      </div>
      <div class="child-box-child-progress" ref="progressBg">
        <div class="child-box-child-progress-content" ref="progressContent">
          <div
            class="child-box-child-progress-content-active"
            ref="progressContentActive"
          ></div>
        </div>
        <div class="child-box-child-progress-limit">
          <div
            class="child-box-child-progress-limit-tab"
            v-for="(data, index) of new Array(3)"
            ref="limit"
          ></div>
          <div
            class="child-box-child-progress-limit-number"
            v-for="(data, index) of ['100%', '150%', '180%', '200%']"
            ref="limitNumber"
          >
            {{ data }}
          </div>
          <div
            class="child-box-child-progress-limit-index"
            v-for="(data, index) of new Array(4)"
            ref="limitIndex"
          ></div>
        </div>
      </div>
    </div>
    <div class="child-box-limit" v-show="awardIndex >= 1">
      <img :src="childBoxLimit" />
    </div>
    <div class="child-box-award" v-show="awardIndex >= 1">
      <div class="child-box-award-bg">
        <img :src="awardBg" />
      </div>
      <div class="child-box-award-text">
        <img :src="awardText[awardTextIndex]" />
      </div>
      <div class="child-box-award-number" ref="awardNumberContent">
        <div class="child-box-award-number-tab">{{ awardNumber[0] }}</div>
        <div class="child-box-award-number-tab">{{ awardNumber[1] }}</div>
        <div class="child-box-award-number-tab">{{ awardNumber[2] }}</div>
        <div class="child-box-award-number-tab">{{ awardNumber[3] }}</div>
      </div>
    </div>
    <div class="child-box-limit" v-show="showVideo">
      <img :src="childBoxLimit" />
    </div>
    <div class="child-box-video" v-show="showVideo">
      <div class="child-box-video-title">
        <img :src="awardTitle" />
      </div>
      <div class="child-box-video-content" ref="content"></div>
    </div>
    <div class="child-box-hot" v-show="childHotShow">
      <img :src="childHotImg[childHotImgIndex]" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Emit, Prop, Ref, Vue, Watch } from 'vue-property-decorator'
import store from '../common/store'
import { InnerOptions, Store } from './item-list/src/types'
import { Color, Img, WoodmanConfig } from '../common/types'
import ItemList from './item-list'
import videoComponent from './video.vue'
import { openSchema } from '../common/utils/js-brige'

@Component({
  name: 'child',
  components: {},
})
export default class child extends Vue {
  @Prop(Object) options!: InnerOptions
  @Prop(Object) item!: { index: number; data: any }
  @Prop(Object) store!: Store

  get config() {
    return store.config
  }

  // 配置加载
  @Watch('config', { deep: true, immediate: true })
  configComplete(newVal: WoodmanConfig) {
    const { color, img } = newVal

    this.colorConfig(color)

    this.imageConfig(img)
  }

  mounted() {
    this.updateLimitOn()
    this.videoListInit()

    this.itemConfig()
  }

  childIcon: string = ''
  childTitle: string = ''
  childHotImgIndex: number = 0
  childHotShow: boolean = false

  progress: number = 0

  awardNumber: number[] = [0, 0, 0, 0]

  showVideo: boolean = false

  jumpLink: string = ''

  itemConfig() {
    const { data } = this.item

    this.childIcon = data.icon
    this.childTitle = data.name

    if (data.hot) {
      this.childHotShow = true
      this.childHotImgIndex = data.hot === 1 ? 0 : 1
    } else {
      this.childHotShow = false
    }

    if (data.awardNumber) {
      this.awardNumber = data.awardNumber
    }

    if (data.progress) {
      const progress = data.progress

      if (progress <= 150) {
        this.progress = progress / 2
      } else if (progress > 150 && progress <= 180) {
        this.progress = 75 + (progress - 150) / 2.4
      } else {
        this.progress = 87.5 + (progress - 180) / 1.6
      }

      this.updateProgressStyle()
    }

    if (data.list.length) {
      this.videoListComponent.setItem(data.list)
      this.showVideo = true
    }

    if (data.link) {
      this.jumpLink = data.link
    }
  }

  @Ref('childBox') childBox: HTMLDivElement

  @Ref('childBorder') childBorder: HTMLDivElement
  @Ref('childTitle') childTitleText: HTMLDivElement

  @Ref('progressBg') progressBg: HTMLDivElement
  @Ref('progressContent') progressContent: HTMLDivElement
  @Ref('progressContentActive') progressContentActive: HTMLDivElement

  @Ref('limit') limit: HTMLDivElement[]
  @Ref('limitNumber') limitNumber: HTMLDivElement[]
  @Ref('limitIndex') limitIndex: HTMLDivElement[]

  @Ref('awardNumberContent') awardNumberContent: HTMLDivElement

  // 加载颜色
  colorConfig(color: Color) {
    if (!color) {
      return
    }

    const { bg, child, progress, award } = color

    this.$nextTick(() => {
      if (bg) {
        this.childBox.style.backgroundColor = bg
      }

      if (child?.border) {
        this.childBorder.style.backgroundColor = child.border
      }
      if (child?.bg) {
        this.childTitleText.style.backgroundColor = child.bg
      }
      if (child?.title) {
        this.childTitleText.style.color = child.title
      }

      if (progress?.border) {
        this.progressBg.style.backgroundColor = progress.border
      }
      if (progress?.bg) {
        this.progressContent.style.backgroundColor = progress.bg
      }
      if (progress?.active) {
        this.progressContentActive.style.backgroundColor = progress.active
      }

      if (progress?.limit || progress?.actLimit) {
        this.limitColor = [progress?.limit || '', progress?.actLimit || '']
        this.updateLimitColor()
      }

      if (progress?.number) {
        for (let i = 0; i < this.limitNumber.length; i++) {
          this.limitNumber[i].style.color = progress.number
        }
      }

      if (progress?.index) {
        for (let i = 0; i < this.limitIndex.length; i++) {
          this.limitIndex[i].style.borderBottomColor = progress.index
        }
      }

      if (award?.number) {
        this.awardNumberContent.style.color = award.number
      }
    })
  }

  childBoxLimit: string = ''

  awardBg: string = ''
  awardText: string[] = new Array(4).fill('')
  awardTitle: string = ''

  childHotImg: string[] = ['', '']

  imageConfig(img: Img) {
    if (!img) {
      return
    }

    if (img?.limit?.url) {
      this.childBoxLimit = img.limit.url
    }

    if (img?.award?.bg?.url) {
      this.awardBg = img.award.bg.url
    }

    const awardText = new Array(5).fill('')
    for (let i = 1; i <= 5; i++) {
      const index = `class${i}` as
        | 'class1'
        | 'class2'
        | 'class3'
        | 'class4'
        | 'class5'

      if (img?.award[index]?.url) {
        awardText[i - 1] = img.award[index].url
      }
    }

    this.awardText = awardText

    if (img?.video?.title?.url) {
      this.awardTitle = img.video.title.url
    }

    if (img?.child) {
      const list = []

      list.push(img.child?.hot?.url || '')
      list.push(img.child?.normal?.url || '')

      this.childHotImg = list
    }
  }

  videoListComponent: ItemList
  @Ref('content') content: HTMLDivElement
  videoListInit() {
    this.videoListComponent = new ItemList({
      el: this.content,
      child: videoComponent,
      itemOptions: {
        width: 3.36,
        top: 0.15,
        left: 0.14,
        row: 2,
      },
      type: 'rem',
    })
  }

  limitOn: [boolean, boolean, boolean] = [false, false, false]
  limitColor: [string, string] = ['', '']
  updateLimitColor() {
    for (let i = 0; i < this.limit.length; i++) {
      this.limit[i].style.backgroundColor = this.limitOn[i]
        ? this.limitColor[1]
        : this.limitColor[0]
    }
  }

  // 更新进度条边界的数组
  updateLimitOn() {
    const limit = [50, 75, 87.5]

    for (let i = 0; i < limit.length; i++) {
      this.limitOn[i] = this.progress >= limit[i]
    }
  }

  // 更新当前档数
  get awardIndex(): number {
    const limit = [50, 75, 87.5, 100]

    let index = 0

    for (let i = 0; i < limit.length; i++) {
      if (this.progress >= limit[i]) {
        index = i + 1
      }
    }

    return index
  }

  // 更新当前挡数 awardText 下标
  get awardTextIndex(): number {
    let index = 0

    if (this.awardIndex !== 0) {
      index = this.awardIndex - 1
    }

    if (this.awardNumber[0] < 20) {
      index = 4
      this.awardNumber[2] = 0
      this.awardNumber[3] = 0
    }

    return index
  }

  // 更新进度条样式
  updateProgressStyle() {
    this.progressContentActive.style.width = `${this.progress}%`
  }

  // 跳转
  jumpLinkClick() {
    openSchema(this.jumpLink)
  }

  //设置
  mySet(data: any) {
    return data
  }

  //获取
  @Emit('myGet')
  myGet(data: any) {
    return data
  }
}
</script>

<style scoped lang="scss">
@import '../common/ViewMaho/base';
.child-box {
  position: relative;
  width: 700px;
  padding-bottom: 20px;
  padding-top: 20px;
  border-radius: 20px;
  @include flexY;
  &-child {
    @include blockRW;
    margin-bottom: 33px;
    @include flexAll;
    height: 158px;
    &-user {
      position: absolute;
      left: 28px;
      width: 172px;
      height: 158px;
      @include flexJustify;
      &-icon {
        position: absolute;
        width: 110px;
        height: 110px;
        left: 28px;
        top: 0;
        @include flexAll;
        border-radius: 50%;
        cursor: pointer;
        &-icon {
          position: absolute;
          width: 103px;
          height: 103px;
          @include flexAll;
          border-radius: 50%;
          overflow: hidden;
          img {
            @include imageCover;
          }
        }
      }
      &-title {
        position: absolute;
        bottom: 0;
        height: 34px;
        font-size: 28px;
        font-weight: bold;
        letter-spacing: 1px;
        @include flexAll;
        padding: 0 20px;
        white-space: nowrap;
      }
    }
    &-progress {
      position: absolute;
      width: 474px;
      height: 45px;
      @include flexAll;
      right: 35px;
      &-content {
        position: absolute;
        width: 468px;
        height: 39px;
        @include flexAll;
        &-active {
          @include blockAH;
          left: 0;
          width: 0;
        }
      }
      &-limit {
        @include blockA;
        @include flexAll;
        &-tab {
          position: absolute;
          height: calc(100% - 6px);
          width: 3px;
          &:nth-child(2) {
            left: calc((100% - 6px) * 0.75 + 3px - 1.5px);
          }
          &:nth-child(3) {
            left: calc((100% - 6px) * 0.87 + 3px - 1.5px);
          }
        }
        &-number {
          position: absolute;
          font-size: 18px;
          bottom: -50px;
          font-weight: bold;
          &:nth-child(5) {
            left: calc(75% - 27px);
          }
          &:nth-child(6) {
            left: calc(87.5% - 27px);
          }
          &:nth-child(7) {
            left: calc(100% - 27px);
          }
        }
        &-index {
          position: absolute;
          width: 0;
          height: 0;
          border: 16px solid transparent;
          bottom: -19px;
          transform: scaleX(0.7);
          &:nth-child(9) {
            left: calc((100% - 6px) * 0.75 + 3px - 16px);
          }
          &:nth-child(10) {
            left: calc((100% - 6px) * 0.875 + 3px - 16px);
          }
          &:nth-child(11) {
            left: calc((100% - 6px) * 1 + 3px - 16px);
          }
        }
      }
    }
  }
  &-limit {
    position: relative;
    width: 683px;
    height: 9px;
    @include flexAll;
    img {
      @include imageContain;
    }
  }
  &-award {
    position: relative;
    width: 100%;
    height: 204px;
    margin-bottom: 33px;
    @include flexAll;
    &-bg {
      position: absolute;
      width: 657px;
      height: 204px;
      @include flexAll;
      img {
        @include imageContain;
      }
    }
    &-text {
      position: absolute;
      width: 164px;
      height: 62px;
      left: 29px;
      top: 24px;
      @include flexAll;
      img {
        @include imageContain;
      }
    }
    &-number {
      @include blockA;
      font-size: 26px;
      @include flexAll;
      font-weight: bold;
      &-tab {
        position: absolute;
        &:nth-child(1) {
          top: 105.5px;
          transform: translateX(-15px);
        }
        &:nth-child(2) {
          top: 105.5px;
          transform: translateX(218px);
        }
        &:nth-child(3) {
          top: 173.5px;
          transform: translateX(-15px);
        }
        &:nth-child(4) {
          top: 173.5px;
          transform: translateX(218px);
        }
      }
    }
  }
  &-video {
    @include blockRW;
    @include flexY;
    &-title {
      @include blockRW;
      margin-top: 16px;
      width: 134px;
      height: 30px;
      margin-bottom: 17px;
      @include flexAll;
      img {
        @include imageContain;
      }
    }
  }
  &-hot {
    position: absolute;
    width: 37px;
    height: 121px;
    left: 13px;
    top: -8px;
    img {
      @include imageContain;
    }
  }
}
</style>
