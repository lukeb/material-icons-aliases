<template lang="pug">
.flex-column
  .header
    .header-contents
      a.github(href='https://github.com/Christilut/material-icons-aliases', target='_blank')
        img(src='/static/images/github.svg')
        .text Contribute!
      .credits Created by
        =" "
        a(href='https://github.com/Christilut', target='_blank') Christiaan Maks
      h1 Material Design Icons
      h2 ... with aliases!
      p I got sick of the Material Design Icons search page which doesn't have any aliases so after failing my search I always ended up scrolling the list.
      p So I present to you: alias search!
      p If you find an alias missing, please don't hesitate to
        =" "
        a(href='https://github.com/Christilut/material-icons-aliases', target='_blank') open a pull request.
  .search(:style='{ "position": searchPosition }')

    i.material-icons search
    input(type='text', v-model='searchValue', placeholder='Search aliases...', autofocus)
  .categories
    .category(v-for='cat in categories', v-if='iconsForCategory(cat).length > 0')
      .title {{ cat }}
      .icons
        .icon(v-for='icon in iconsForCategory(cat)', :title='aliasString(cat, icon)')
          i.material-icons {{ sanitizeIconName(icon) }}
          .name {{ icon }}
  .footer
    p You can get the Material Design Icons on the
      =" "
      a(href='https://material.io/icons/', target='blank') official Google page.
    p Material Design Icons are property of Google and this website is not affiliated with Google in any way.
</template>

<script>
const BAD_WORDS = [ // filter some problematic/empty icons
  '0 bar',
  '1 bar',
  '2 bar',
  '3 bar',
  'battery 20',
  'battery 30',
  'battery 50',
  'battery 60',
  'battery 80',
  'battery 90',
  'battery charging 20',
  'battery charging 30',
  'battery charging 50',
  'battery charging 60',
  'battery charging 80',
  'battery charging 90',
  '26x'
]

export default {
  data () {
    return {
      searchValue: '',
      iconIndex: require('src/../icons.yml'),
      searchPosition: 'relative'
    }
  },
  computed: {
    categories () {
      return Object.keys(this.iconIndex)
    }
  },
  methods: {
    iconsForCategory (cat) {
      let icons = Object.keys(this.iconIndex[cat])

      icons = icons.filter(x => !BAD_WORDS.some(y => x.indexOf(y) !== -1))

      if (this.searchValue) {
        const searchTerms = this.searchValue.toLowerCase().split(' ').filter(x => x !== '')

        icons = icons.filter(i => {
          return searchTerms.every(st => i.indexOf(st) !== -1 || this.getAliasArray(cat, i).some(a => a.indexOf(st) !== -1))
        })
      }

      return icons
    },

    sanitizeIconName (icon) {
      return icon.replace(/ /g, '_')
    },

    getAliasArray (cat, icon) {
      return this.iconIndex[cat][icon] || []
    },

    aliasString (cat, icon) {
      return [icon].concat(this.getAliasArray(cat, icon)).join(', ')
    }
  },
  mounted () {
    window.addEventListener('scroll', (e) => {
      if (window.innerWidth > 600 ? window.pageYOffset > 360 : window.pageYOffset > 550) {
        this.searchPosition = 'fixed'
      } else {
        this.searchPosition = 'relative'
      }
    })
  }
}
</script>

<style lang="scss" scoped>
@import 'src/styles/variables';
@import 'src/styles/layout';

$headerHeight: 300px;
$headerHeightPhone: 430px;
$gutter: 30px;
$textColor: rgba(255, 255, 255, 0.9);

.header {
  height: $headerHeight;
  background: $primary;
  color: $textColor;
  padding: $gutter $gutter * 2;

  @include phone {
    padding: 100px 20px 20px 20px;
    height: $headerHeightPhone;
  }

  .header-contents {
    position: relative;
    max-width: 1200px;
    margin: 0 auto;
    height: 100%;
  }

  .credits {
    position: absolute;
    right: $gutter;
    bottom: 20px;
  }

  .github {
    position: absolute;
    height: 60px;
    width: 60px;
    right: $gutter;
    top: 0;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    transition: transform 0.2s ease-in-out;

    &:hover {
      transform: scale(1.2);
    }

    &:after {
      background: none;
    }

    @include phone {
      top: -90px;
    }

    img {
      height: 100%;
      width: 100%;
    }

    .text {
      position: relative;
      top: -5px;
      font-size: 14px;
      color: $textColor;
      border: none;
    }
  }
}

.search {
  display: flex;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 10;
  height: 0;

  i {
    position: absolute;
    left: 10px;
    top: 15px;
    font-size: 36px;
    color: #888;
  }

  input {
    display: flex;
    flex-grow: 1;
    height: 60px;
    padding-left: 50px;
    border: none;
    box-shadow: 0 4px 5px 0 rgba(0, 0, 0, 0.14), 0 1px 10px 0 rgba(0, 0, 0, 0.12), 0 2px 4px -1px rgba(0, 0, 0, 0.2);
    font-size: 20px;

    &:focus {
      outline: none;
    }
  }
}

.categories {
  margin-top: 50px;
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;

  .category {
    padding: 70px 30px 30px 30px;
    position: relative;

    @include phone {
      padding: 70px 10px 30px 10px;
    }

    .title {
      text-transform: uppercase;
      font-weight: bold;
      text-align: center;
    }

    .icons {
      display: flex;
      flex-wrap: wrap;
      width: 100%;
      justify-content: center;

      .icon {
        display: flex;
        flex-direction: column;
        margin: 20px 10px 10px 10px;
        align-items: center;
        width: 150px;

        @include phone {
          width: 120px;
        }

        i {
          color: #888;
          font-size: 50px;
        }

        .name {
          font-size: 12px;
          // white-space: nowrap;

          margin-top: 5px;
        }
      }
    }
  }
}

.footer {
  height: 200px;
  background: $primary;
  color: $textColor;
  padding: $gutter;
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

</style>
