<template>
  <div class="utilityLibrary-box" @click="testClick"></div>
</template>

<script lang="ts">
import { Component, Emit, Vue } from 'vue-property-decorator'
// import MyComponent from '@bilibili-activity/utility-library'

@Component({
  name: 'utilityLibrary',
  components: {},
})
export default class utilityLibrary extends Vue {
  mounted() {
    const a = 1
    // const myComponent = new MyComponent()
  }

  testClick() {
    // @ts-ignore window exp
    console.log(window.biliSDK)
    // @ts-ignore window exp
    // window.biliSDKbiliSDK.biliBridge.callNative({
    //   method: 'ability.saveImageToPhotosAlbum',
    //   data: {
    //     filePath:
    //       'https://i0.hdslb.com/bfs/activity-plat/static/20220621/70bb422013eb4692db31a31f5240742d/oQEobu5OKm.gif',
    //   },
    //   callback: (info: any) => {
    //     console.log(info)
    //   },
    // })

    window.biliSDK.biliBridge.callNative({
      // method: 'share.setShareMpcContent',
      method: 'share.showShareMpcWindow',
      data: {
        onShareCallbackId: '123',
        // 自定义分享渠道和顺序
        // 客户端对于动态渠道有特殊逻辑, 如果在登录状态下，shareChannelQueue 中有dynamic渠道，会默认展示在第一个位置，如果未登录状态下即使有dynamic渠道也不会展示
        // 支持渠道参数：['generic'，'dynamic'，'weixin'，'weixin_monment'，'qq'，'sina'，'q_zone'，'copy']
        shareChannelQueue: ['weixin', 'q_zone', 'weixin_monment', 'dynamic'],
        share_id: '', // 如专栏 read.column-detail.0.0.pv
        share_origin: '', // 分享来源, 同一个share_id下, 区分场景使用
        oid: '', // 如专栏 文章id
        sid: '', // 辅助id
        generic: {
          type: 'image', // (必需，包含'text', 'image')
          title: '分享标题', // (必需)
          imageUrl: '图片链接', // (必需)
          text: '描述一下', // (必需)
        },
        dynamic: {
          title: '分享标题', // (必需)
          content_type: 2048, // (必需)
          cover_url: '封面图', // (必需)
          description: '描述描述描述描述描述描述描述', // (必需)
          sketch: {
            // (非必需) sketch字段结构参考: https://info.bilibili.co/pages/viewpage.action?pageId=5429370
            title: '',
            desc_text: '',
            biz_type: 1,
            cover_url: '',
            target_url: '',
          },
          content_id: 1, // (非必需)
          author_id: 123, // (非必需)
          author_name: '', // (非必需)
          images: ['', '', ''], // (非必需)
          publish: false, // (非必需)
          edit_content: '11111111111', // (非必需)
          repost_code: 123, // (非必需)
          images_online: '', // (非必需)
          biz_id: Number, // (非必需；附加卡业务方id)  6.7+版本支持
          biz_type: Number, // (非必需；附加卡业务方类型)  6.7+版本支持
        },
        weixin: {
          type: 'image', // (必需, 包含'text', 'image', 'video', 'audio', 'web', 'min_program')
          title: '分享标题', // (必需)
          text: '描述一下', // (必需)
          url: '分享链接', // (必需)
          imageUrl:
            'http://b.hiphotos.baidu.com/baike/pic/item/a9d3fd1f4134970a39018fca97cad1c8a6865de0.jpg', // (type为image类型必需)
          program_id: '123', // (type为min_program类型必需，该id是小程序的原始id, 类似于gh_xxxxxxxx)
          program_path: 'path', // (type为min_program类型必需)
          media_src: '多媒体地址', //(type为video audio类型必需)
        },
        weixin_monment: {
          type: 'image', // (必需, 包含'text', 'image', 'video', 'audio', 'web')
          title: '分享标题', // (必需)
          text: '描述一下', // (必需)
          url: '分享链接', // (必需)
          imageUrl:
            'http://b.hiphotos.baidu.com/baike/pic/item/a9d3fd1f4134970a39018fca97cad1c8a6865de0.jpg', // (type为image类型必需)
          media_src: '多媒体地址', // (type为video audio类型必需)
        },
        qq: {
          type: 'image', // (必需, 包含'text', 'image', 'video', 'audio', 'web')
          title: '分享标题', // (必需)
          text: '描述一下', // (必需)
          url: '分享链接', // (必需)
          imageUrl:
            'http://b.hiphotos.baidu.com/baike/pic/item/a9d3fd1f4134970a39018fca97cad1c8a6865de0.jpg', // (type为image类型必需)
          media_src: '多媒体地址', // (type为video audio类型必需)
        },
        sina: {
          type: 'image', // (必需, 包含'text', 'image', 'video', 'audio', 'web')
          title: '分享标题', // (必需, 新版新浪客户端不会识别该参数, 但是分享组件里需要识别该参数，业务方可以传自己title或传'哔哩哔哩 (゜-゜)つロ 干杯~')
          text: '描述一下', // (必需)
          url: '分享链接', // (必需)
          imageUrl:
            'http://b.hiphotos.baidu.com/baike/pic/item/a9d3fd1f4134970a39018fca97cad1c8a6865de0.jpg', // (type为image类型必需)
          media_src: '多媒体地址', // (type为video audio类型必需)
        },
        q_zone: {
          type: 'image', // (必需, 包含'text', 'image', 'web')
          title: '分享标题', // (必需)
          text: '描述一下', // (必需)
          url: '分享链接', // (必需)
          imageUrl:
            'http://b.hiphotos.baidu.com/baike/pic/item/a9d3fd1f4134970a39018fca97cad1c8a6865de0.jpg', // (type为image类型必需)
        },
        copy: {
          type: 'image', // (必需, 包含'text', 'image', 'video', 'audio', 'web', copy渠道可以填写'text')
          title: '分享标题', // (必需)
          url: '', // (必需)
        },
        default: {
          type: 'image', // (必需, 包含'text', 'image', 'video', 'audio', 'web')
          title: '分享标题', // (必需)
          text: '描述一下', // (必需)
          url: '分享链接', // (必需)
          imageUrl:
            'http://b.hiphotos.baidu.com/baike/pic/item/a9d3fd1f4134970a39018fca97cad1c8a6865de0.jpg', // (type为image类型必需)
          media_src: '多媒体地址', // (type为video audio类型必需)
        },
      },
      callback: () => {},
    })
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
.utility-library-box {
  position: absolute;
  top: 0;
  width: 500px;
  height: 500px;
  background: #6c5ce7;
}
</style>
