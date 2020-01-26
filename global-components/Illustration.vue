<script>
export default {
  functional: true,

  props: {
    src: {
      type: String,
      default: './test.jpg'
    }
  },

  render(h, { props, slots }) {
    return h(
      'img',
      {
        class: ['py-s', 'px', 'mb', 'bg-alt'],
        attrs: {
          src: props.src
        }
      },
      slots().default
    )
  }
}
</script>
