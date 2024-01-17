<template>
  <div>
    <div id="vk-widget" @scroll="handleScroll">
      <div id="post-item" v-for="post in posts" :key="post.id">{{ post.text }}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      accessToken: 'vk1.a.mICXdsnb1Wdnjf1Kr3YLCvx2H85fPujqdcdiCwp-ceftCeIzmmWPgGrNXzNVBUCBFpxR_JQlHdFP4fs3XFUU2BYcZaHF6IXBbVRAwSNv-ZeS-M3RqZOJAXBXTXlswkOZkHyc-HVwzr0CxSm52DgDWKrnhfc0-hMQjIFSrb-0NMZPJEOgEc2xBORYrz-oPBoKbAKdePOxxjFnCuptg_SJxA&expires_in=86400&user_id=331418212',
      domain: 'anekdotikategoriib',
      offset: 0,
      posts: []
    };
  },
  methods: {
    async loadPosts() {
      const callbackName = 'jsonpCallback';
      const script = document.createElement('script');
      script.src = `https://api.vk.com/method/wall.get?access_token=${this.accessToken}&domain=${this.domain}&count=10&offset=${this.offset}&v=5.131&callback=${callbackName}`;
      document.body.appendChild(script);

      window[callbackName] = (data) => {
        document.body.removeChild(script);
        delete window[callbackName];

        if (data.response && data.response.items) {
          this.posts = this.posts.concat(data.response.items);
          this.offset += 10;
        }
      };
    },
    handleScroll() {
      const element = document.getElementById('vk-widget');
      if (element.scrollTop + element.clientHeight >= element.scrollHeight) {
        this.loadPosts();
      }
    }
  },
  mounted() {
    const cachedPosts = localStorage.getItem('cachedPosts');
    if (cachedPosts) {
      this.posts = JSON.parse(cachedPosts);
    }

    this.loadPosts();
  },
  watch: {
    posts() {
      localStorage.setItem('cachedPosts', JSON.stringify(this.posts));
    }
  }
};
</script>

<style>
#vk-widget {
  width: 500px;
  height: 800px;
  overflow-y: scroll;
  border: 0px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
#post-item {
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  background-color: #fff;
  border-radius: 15px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
</style>