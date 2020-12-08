<template>
  <div class="home">
    <div class="content-itens">
      <b-container>
        <b-row>
          <b-col cols="12">
            <div class="d-flex justify-content-between align-items-center">
              <div class="container-upload d-flex align-items-center">
                <label class="label-upload d-flex justify-content-center align-items-center" for="upload-file">Upload <span class="badge badge-light ml-1">{{ imgs.length }}</span></label>
                <label class="label-limpar d-flex flex-column justify-content-center" @click="limparUpload">Limpar</label>
                <a class="d-flex flex-column justify-content-center" @click="cssText" :href="link" download="icons.css">Gerar Arquivo</a>
              </div>
              <input id="upload-file" ref="upload" multiple @change="loadFile" v-bind="file" type="file" accept="image/svg+xml">
              <input class="input-search" v-model="search" type="text">
            </div>
          </b-col>
        </b-row>
      </b-container>
    </div>
    <b-container class="container-icons">
      <b-row>
        <b-col cols="12" class="d-flex flex-wrap">
          <template v-for="(el, i) in arrayIcons">
            <div class="container-img mt-5 d-flex flex-column align-items-center justify-content-between" :key="i">
              <img :src="el.file" alt="">
              <input class="text-truncate text-center" @click="copiar" :value="`icon-r-${el.name}`" type="text" readonly :title="`icon-r-${el.name}`">
              <a @click.prevent="toast(el.name, el.file)" href="">Exibir Base64</a>
            </div>
          </template>
        </b-col>
        <div v-if="arrayIcons.length == 0" class="w-100 p-5">
            <img src="@/assets/tabela_vazia.svg" alt="">
        </div>
      </b-row>
    </b-container>
  </div>
</template>

<script lang="ts">
import Vue from 'vue';

export default Vue.extend({
  name: 'Home',
  data() {
    return {
      data: [],
      file: {},
      link: '',
      imgs: [],
      search: '',
    };
  },
  computed: {
    arrayIcons(): any[] {
      if (this.search === '' || this.search === null) {
        return this.imgs;
      }
      return this.imgs.filter((item: any): boolean => {
        return item.name.includes(this.search);
      });
    }
  },
  methods: {
    loadFile(e: Event) {

      for (let i = 0; i < (e.target as HTMLInputElement).files.length; i += 1) {
        const reader = new FileReader();
        let aux = false;
        reader.onloadend = () => {
          //Verifica se já existe a imagem
          for(let j = 0; j < this.imgs.length; j += 1) {
            if( this.imgs[j].file === reader.result) {
              aux = true;
              break;
            }
          }
          //Insere a imagem caso ainda não exista
          if(!aux) {
            this.imgs.push({
              file: reader.result, name: this.textFile((e.target as HTMLInputElement).files[i].name),
            });
            this.imgs.sort((a: any, b: any): number => {
              return (a.name > b.name) ? 1 : ((b.name > a.name) ? -1 : 0);
            });
          }
        };
        console.log((e.target as HTMLInputElement).files[i]);
        reader.readAsDataURL((e.target as HTMLInputElement).files[i]);
      }
    },
    textFile(value: string) {
      return value.split('.')[0];
    },
    toast(tilte: string, content: string) {
      this.$bvToast.toast(content, {
        title: `icon-r-${tilte}`,
        toaster: 'b-toaster-bottom-full',
        solid: true,
      })
    },
    copiar(e:Event) {
      console.log((e.target as HTMLInputElement));
      (e.target as HTMLInputElement).select();
      document.execCommand('copy');
    },
    limparUpload() {
      (this.$refs.upload as HTMLInputElement).value = '';
      this.imgs = [];
    },
    cssText() {
      let text = `[class^="icon-r-"], [class*=" icon-r-"] {\n display: inline-block;\n mask-repeat: no-repeat;\n mask-size: 100% 100%;\n mask-position: center;\n -webkit-mask-repeat: no-repeat;\n -webkit-mask-size: 100% 100%;\n -webkit-mask-position: center;\n width: 20px;\n height: 20px;\n background: #000;\n}\n\n`

      if(this.imgs.length > 0) {
        for(let value of this.imgs) {
          text += `.icon-r-${value.name} {\n mask-image: url('${value.file}');\n -webkit-mask-image: url('${value.file}');\n}\n\n`;
        }
      }

      const blob = new Blob([text], { type: 'octet/stream' });
      this.link = window.URL.createObjectURL(blob);
    }
  },
});
</script>
<style lang="scss">
.container-icons {
  padding-top: 60px;
  padding-bottom: 60px;
  input {
    background: #eff1f4;
  }
}
div.container-img {
  width: 16.666666666666668%;
  height: 75px;
  border-width: 1px;
  border-radius: 10px;
  input {
    max-width: 90%;
    height: 20px;
    border: none;
  }
  img {
    height: 30px;
    max-width: 20%;
  }
  a {
    height: 5px;
  }
}
input[type='file'] {
  display: none;
}
.input-search {
  height: 35px;
  border-radius: 5px;
  border: solid 1px #ced4da;
  background-image: url('data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNC4wMDEiIGhlaWdodD0iMjQiIHZpZXdCb3g9IjAgMCAyNC4wMDEgMjQiPjxkZWZzPjxzdHlsZT4uYXtmaWxsOiMwODAyMDI7fTwvc3R5bGU+PC9kZWZzPjxwYXRoIGNsYXNzPSJhIiBkPSJNMTguMDI2LDkuMDEzYTkuMDEzLDkuMDEzLDAsMSwwLTkuMDEzLDkuMDEzLDkuMDEzLDkuMDEzLDAsMCwwLDkuMDEzLTkuMDEzWm0tOS4wMTMsNi43NmE2Ljc2LDYuNzYsMCwxLDEsNi43NjEtNi43Niw2Ljc2LDYuNzYsMCwwLDEtNi43NjEsNi43NloiLz48cGF0aCBjbGFzcz0iYSIgZD0iTTMwNC45MzcsMzAxLjc1OGwtNS41MTQtNS41MTVhMTAuNjE0LDEwLjYxNCwwLDAsMS0zLjE4NywzLjE4Nmw1LjUxNSw1LjUxNWEyLjI1MywyLjI1MywwLDAsMCwzLjE4Ni0zLjE4NloiIHRyYW5zZm9ybT0idHJhbnNsYXRlKC0yODEuNTYgLTI4MS41NjkpIi8+PC9zdmc+');
  background-repeat: no-repeat;
  background-position: 10px center;
  padding-left: 45px;
  outline: none;
  color: #2c3e50;
}
.container-upload {
  width: fit-content;
  label {
    cursor: pointer;
    height: 35px;
    min-width: 100px;
    margin-bottom: 0px;
    padding: 0px 10px;
  }
  a {
    height: 35px;
    width: 120px;
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
    background: #e9ecef;
    border: solid #ced4da;
    border-width: 1px 1px 1px 1px;
    color: #2c3e50;
    text-decoration: none;
    &:hover {
      color: #2c3e50;
      text-decoration: none;
    }
  }
  .label-upload {
    background: #E2B874;
    color: #fff;
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
  }
  .label-limpar {
    background: #e9ecef;
    border: solid #ced4da;
    border-width: 1px 0px;
  }
}
.toast-body {
  overflow-x: auto;
}
.content-itens {
  position: fixed;
  width: 100%;
  z-index: 1;
  background: #113F5D;
  padding: 10px 0px;
}

@media (max-width: 991px) {
  .container-upload {
    p {
      max-width: 120px;
    }
  }
}
</style>
