<template>
  <div >
    <div class="row main">
      <div class="col-md-6">
      <textarea class="form-control" v-model="source"></textarea>
      </div>
      <div class="col-md-6">
      <textarea  class="form-control" readonly v-model="converted"></textarea>
      </div>
    </div>
  </div>
</template>

<script>
const pretty = require('pretty');
const htmlEscape = (str) => {
    if (!str) return;
    return str.replace(/[<>&"'`]/g, (match) => {
        const escape = {
            '<': '&lt;',
            '>': '&gt;',
            '&': '&amp;',
            '"': '&quot;',
            "'": '&#39;',
            '`': '&#x60;'
        };
        return escape[match];
    });
}
export default {
  components: {

  },
  computed:{
    converted() {
        let source = this.source;
        source = source.replace(/ *?@end(.+?\b)(\(.+\))?/g, (all, a, b)  => {
            return '</blade-'+a+(b ? 'data="'+htmlEscape(b)+'"' : '')+'>'
        });

        source = source.replace(/ *@(\b.+?\b)(\(.+\))?/g, (all, a, b) => {
            let self_close = true;
            if (this.source.match('@end'+a)) {
                self_close = false;
            }
            return '<blade-'+a+(b ? ' data="'+htmlEscape(b)+'"' : '')+(self_close ? "/" : "")+'>'
        });

        source = pretty(source)


        source = source.replace(/<blade-(\b.+?\b)( data="(.+?)")?( \/)?>/g, (all, a, b, c) => {
            return '@'+a+ (c ? c :'')
        })

        source = source.replace(/<\/blade-(\b.+?\b)( data="(.+?)")?>/g, (all, a, b, c) => {
            return '@end'+a+ (c ? c :'')
        })
        return source;
    }
  },
  data(){
      return {
          source: '@if(true === (true)) @else @endif',

      }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.main textarea {
  height: 100%;
  display: block;
}
.main {
  height: 80vh;
}
html,body {
  height: 100%;
}
</style>
