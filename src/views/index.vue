<template>
  <div class="index">
    <div class="head">
      <h1 class="head_title">ui_beamの图标库</h1>
      <h4 class="head_txt">
        提供在线图标链接，用于个人设备显示使用，禁止用于商业用途
      </h4>
      <h4 class="head_txt">开源项目，By ui_beam，基于<a href="https://github.com/Siriling/my-icons" target="_blank">Siriling/my-icon</a>&<a
          href="https://gitee.com/heizicao/my-icon" target="_blank">heizicao/my-icon</a>修改</h4>
      <h4 class="head_txt">下载来源可选：jsDelivr、123云盘直链、GitHubProxy、服务器直链</h4>
      <h4 class="head_txt">图标最近更新时间：2024-8-4 15:59:06</h4>
      <div class="use">
        <el-image class="use_img" :src="require('../assets/docker.png')" />
        <div class="use_txt">Docker容器</div>
        <el-divider direction="vertical" />
        <el-image class="use_img" :src="require('../assets/router.png')" />
        <div class="use_txt">路由器</div>
        <el-divider direction="vertical" />
        <el-image class="use_img" :src="require('../assets/vms.png')" />
        <div class="use_txt">虚拟机</div>
        <el-divider direction="vertical" />
        <el-image class="use_img" :src="require('../assets/synology.png')" />
        <div class="use_txt">群晖</div>
        <el-divider direction="vertical" />
        <el-image class="use_img" :src="require('../assets/project.png')" />
        <div class="use_txt">项目</div>
        <el-divider direction="vertical" />
        <el-image class="use_img" :src="require('../assets/other.png')" />
        <div class="use_txt">其他</div>
      </div>
      <el-input v-model="data.search" placeholder="请输入你要搜索的图片名称，例如：Acme" class="search" size="large"
        @keyup.enter="handleSearch">
        <template #prepend>
          <el-select class="select" @change="handleSelect" v-model="data.selectvalue" placeholder="全部"
            style="width: 115px" size="large" clearable>
            <el-option label="容器" value="1" />
            <el-option label="路由器" value="2" />
            <el-option label="虚拟机" value="3" />
            <el-option label="群晖" value="4" />
            <el-option label="项目" value="5" />
            <el-option label="其他" value="6" />
          </el-select>
        </template>
      </el-input>
      <el-button class="bt_search" :icon="Search" @click="handleSearch" round size="large"
        color="#1E90FF">搜索</el-button>
    </div>
    <div class="content">
      <div class="content_head">
        <div class="content_title">{{ data.selectlabel }}</div>
        <div class="content_sum">{{ data.icondata.length }}</div>
        <el-select class="cdn-select" v-model="value1" placeholder="请选择图标下载来源" style="margin-left: 20px;">
          <el-option label="jsDelivr" value="JsDelivr"></el-option>
          <el-option label="123云盘直链" value="123pan"></el-option>
          <el-option label="GitHubProxy" value="ghproxy"></el-option>
          <el-option label="服务器" value="server"></el-option>
        </el-select>
      </div>
      <el-space wrap>
        <div v-for="(item, index) in data.icondata" :key="index" class="card" @click="handleClick(item.name + '.png')">
          <el-image lazy class="card_img" :src="data.publicPath + 'icon/' + item.name + '.png'" />
          <!--默认使用从本地读取图标展示在网页上-->
          <!-- 从CDN读取图片展示在网页上, 直接把data.publicPath链接替换CDN链接即可，注意添加链接时需要加入单引号-->
          <!--用法示例：:src="'https://cdn.jsdelivr.net/gh/user/repo@version/file' + 'icon/' + item.name + '.png'" />-->
          <div class="card_txt" @click="openUrl(item.course)">
            {{ item.name }}
          </div>
        </div>
      </el-space>
    </div>
    <div class="foot">
      <div class="foot_txt">© 2024.08.03 | By ui_beam</div>
      <div class="foot_url">
        <el-popover placement="top" :width="150">
          <template #reference>
            <el-image class="foot_img" :src="require('../assets/wechat.png')" />
          </template>
          <el-image class="qrcode_img" :src="require('../assets/qrcode.png')" />
        </el-popover>
        <el-image class="foot_img" :src="require('../assets/gitee.png')"
          @click="openUrl('https://gitee.com/ui_beam/my-icons')" />
        <!--改为你的码云项目地址-->
        <el-image class="foot_img" :src="require('../assets/github.png')"
          @click="openUrl('https://github.com/shigureui9/my-icons')" />
        <!--改为你的GitHub项目地址-->
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive, onMounted, ref } from "vue";
import { Search } from "@element-plus/icons-vue";
import { ElMessage } from "element-plus";
import clipboard from 'vue-clipboard3';
import CryptoJS from 'crypto-js';

export default defineComponent({
  setup() {
    const { toClipboard } = clipboard();
    const value1 = ref<string>();
    const data = reactive({
      selectlabel: "全部",
      selectvalue: "",
      search: "",
      originalIcondata: [] as any[],
      icondata: [] as any[],
      publicPath: process.env.BASE_URL,
      categories: [
        { label: "全部", value: "", sort: "" },
        { label: "容器", value: "1", sort: "docker" },
        { label: "路由器", value: "2", sort: "router" },
        { label: "虚拟机", value: "3", sort: "vms" },
        { label: "群晖", value: "4", sort: "synology" },
        { label: "项目", value: "5", sort: "project" },
        { label: "其他", value: "6", sort: "other" },
      ],
    });

    onMounted(async () => {
      data.originalIcondata = await readLocalFile();
      data.icondata = [...data.originalIcondata];
    });

    async function readLocalFile(): Promise<any[]> {
      return fetch(data.publicPath + "db.json?" + new Date().getTime())
        .then((response) => response.json())
        .then((data) => {
          data.sort((a: any, b: any) => a.name.localeCompare(b.name));
          return data;
        })
        .catch((error) => {
          throw error;
        });
    }

    function handleSelect(value: string) {
      const category = data.categories.find(c => c.value === value);
      if (category) {
        data.selectlabel = category.label;
        data.icondata = filterByCategory(category.sort);
      }
    }

    function filterByCategory(sort: string): any[] {
      const tempdata = [...data.originalIcondata];
      return sort ? tempdata.filter(item => item.sort === sort) : tempdata;
    }

    async function handleSearch() {
      const keyword = data.search.toLowerCase();
      const tempdata = [...data.originalIcondata];
      const filteredData = filterByCategory(data.categories.find(c => c.value === data.selectvalue)?.sort || "")
        .filter(item => item.name.toLowerCase().includes(keyword));
      data.icondata = filteredData;
    }

    async function handleClick(url: string) {
      const currentUrl = window.location.href;
      const iconUrl = `icon/${url}`;
      const iconUrlCdn = `https://vip.123pan.cn`;
      // 123云盘CDN地址
      const iconUrlJsDelivr = `https://cdn.jsdelivr.net/gh/ui-beam-9/my-icons/public/${iconUrl}`;
      // JsDelivr地址
      const iconUrlghproxy = `https://mirror.ghproxy.com/https://raw.githubusercontent.com/ui-beam-9/my-icons/main/public/${iconUrl}`;
      // GitHub地址，这里默认已经配置了GitHub的代理地址
      const serverUrl = `${currentUrl.split('#')[0]}${iconUrl}`;

      const sources = {
        '123pan': iconUrlCdn,
        'JsDelivr': iconUrlJsDelivr,
        'ghproxy': iconUrlghproxy,
        'server': serverUrl,
      };

      let finalUrl = '';

      if (value1.value && value1.value in sources) {
        finalUrl = sources[value1.value];

        if (value1.value === '123pan') {
          // 当选择了123pan时，生成鉴权URL
          const uid = 123456;
          // 123云盘的UID
          const path = `image/icon/${url}`;
          // 123云盘的图片路径，这里默认是icon目录，改成自己的目录
          const timestamp = 8299472400;
          // 123云盘的过期时间戳，这里默认时间到2233-01-01 01:00:00
          const rand = Math.floor(Math.random() * 1000000); // 生成一个随机数
          const authKey = 'xxxxxxxx';
          // 123云盘鉴权密钥

          // 计算md5hash
          const md5Hash = CryptoJS.MD5(`/${uid}/${path}-${timestamp}-${rand}-${uid}-${authKey}`).toString();

          // 构建完整的URL
          const fullUrl = `${iconUrlCdn}/${uid}/${path}?auth_key=${timestamp}-${rand}-${uid}-${md5Hash}`;
          finalUrl = fullUrl;
          //console.log(finalUrl);
        }
      } else {
        console.error('读取图标数据出错，请按照提示进行操作!');
        ElMessage({
          message: "请选择图标下载来源后重试！",
          type: "warning",
        });
        return;
      }
 

      //if (finalUrl === '') {
      //  finalUrl = ''; // 无效的 URL
      //}

      try {
    const success = await toClipboard(finalUrl);
    if (success) {
      ElMessage({
        message: "图标链接复制成功！按Ctrl+V粘贴！",
        type: "success",
      });
    }
  } catch (error) {
    if (!window.confirm("图标链接无法复制到剪切板，请检查浏览器设置中网站权限是否允许使用剪切板！")) {
      return;
    }
  }
}
    function openUrl(url: string) {
      window.open(url, "_blank");
    }

    return {
      data,
      value1,
      Search,
      handleClick,
      handleSearch,
      handleSelect,
      openUrl,
    };
  },
});
</script>

<style lang="scss">
.index {
  height: 100%;
}

.head {
  padding: 50px;
  background-color: #f5f7fc;
}

.head_title {
  font-weight: 400;
  margin-bottom: 20px;
}

.head_txt {
  color: #5d667a;
  font-weight: 400;
  margin-bottom: -10px;
}

.use {
  margin-top: 50px;
  color: #5d667a;
  display: flex;
  align-items: center;
}

.use_img {
  width: 30px;
  height: 30px;
}

.use_txt {
  font-size: 16px;
  margin-left: 10px;
  margin-right: 20px;
}

.el-divider {
  margin-left: 10px;
  margin-right: 20px;
}

.search {
  margin-top: 50px;
  width: 400px;
}

.search .el-input__wrapper {
  border-radius: 0 50px 50px 0 !important;
  font-size: auto;
  font-size: 13px;
}

.select .el-input__wrapper {
  border-radius: 50px 0 0 50px !important;
  font-size: inherit;
}

.el-input-group__prepend {
  box-shadow: none;
}

.bt_search {
  margin-top: 50px;
  margin-left: 10px;
  color: #ffffff;
}

.bt_search:hover {
  color: #ffffff;
}

.content {
  padding: 50px;
  min-height: calc(100% - 535px);
}

.content_head {
  display: flex;
  align-items: center;
  margin-bottom: 30px;
}

.content_title {
  font-weight: 600;
  font-size: 18px;
  color: #1a2947;
}

.content_sum {
  padding: 3px 5px;
  border-radius: 5px;
  font-size: 14px;
  margin-left: 8px;
  color: #5d667a;
  background-color: #f5f7fc;
}

.card {
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 90px;
  width: 90px;
  border-radius: 10px;
  border: 1px solid #f5f7fc;
}

.card:hover {
  // border: 1px solid #f5f7fc;
  box-shadow: 0 8px 24px #1a29470a, 0 2px 8px #1a294714;
}

.card_img {
  width: 45px;
  height: 45px;
}

.card_txt {
  text-align: center;
  color: #afb7c7;
  margin-top: 2px;
  font-size: 12px;
  width: 80px;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.foot {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-bottom: 20px;
  font-size: 13px;
  color: #5d667a;
  // position: relative;
}

.foot_txt {
  margin-right: 50px;
  font-weight: lighter;
}

.foot_url {
  position: absolute;
  right: 80px;
}

.qrcode_img {
  width: 125px;
  height: 125px;
}

.foot_img {
  cursor: pointer;
  width: 18px;
  height: 18px;
  margin: 0 15px;
  // opacity: 0.6;
}

.switch {
  padding-left: 25px;
}
</style>
