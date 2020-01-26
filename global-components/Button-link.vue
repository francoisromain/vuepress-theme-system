<script>
export default {
  functional: true,

  props: {
    href: {
      type: String,
      default: 'http://example.com'
    }
  },

  render(h, { props, slots }) {
    return h(
      'a',
      {
        class: ['btn-border', 'py-s', 'px', 'mb', 'mr-s'],
        attrs: {
          href: props.href
        }
      },
      slots().default
    )
  }
}
</script>
