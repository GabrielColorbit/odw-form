<script>
import Label from "./Label"
import Messages from "./Messages"
import State from "./State"
import Value from "./Value"

import isArray from "lodash/isArray"

export default {
  components: {
    [Label.name]: Label
  },
  extends: Value,
  props: {
    isHidden: {
      type: Boolean,
      default: false
    },
    isLocked: {
      type: Boolean,
      default: false
    },
    isValid: {
      type: Boolean,
      default: true
    },
    isMultiple: {
      type: Boolean,
      default: false
    },
    label: {
      type: String,
      required: true
    },
    name: {
      type: String,
      required: true,
      validator(name) {
        return Boolean(name)
      }
    },
    messages: {
      type: Object,
      validator(messages) {
        return true // !messages || messages instanceof Messages
      },
      default() {
        return new Messages()
      }
    },
    options: {
      type: Object,
      default() {
        return {}
      }
    }
  },
  data() {
    return {
      state: State.RESETED
    }
  },
  methods: {
    // Value manipulation
    set(value = null, dirty = true) {
      this.state = dirty ? State.set(this.state, State.DIRTY) : State.unset(this.state, State.CHANGED)

      if (this.isMultiple) {
        this.$emit("input", isArray(value) ? value : new Array(value))
      } else {
        this.$emit("input", isArray(value) ? value[0] : value)
      }
    },
    get() {
      // return this.vuex ? this.state[this.name] : this.value
      return this.value
    },
    format() {
      return `${this.isMultiple ? this.get().join(", ") : this.get()}`
    },
    reset() {
      this.state = State.set(this.state, State.RESETED)
      this.state = State.unset(this.state, State.CHANGED)
      this.set(this.default)
    },

    // Valid
    valid() {
      this.state = State.set(this.state, State.VALID)
      this.state = State.unset(this.state, State.UNVALID)
    },
    invalid() {
      this.state = State.unset(this.state, State.VALID)
      this.state = State.set(this.state, State.UNVALID)
    },

    // Display
    hide() {
      this.state = State.set(this.state, State.HIDDEN)
    },
    show() {
      this.state = State.unset(this.state, State.HIDDEN)
    },

    // Lock
    lock() {
      this.state = State.set(this.state, State.LOCKED)
    },
    unlock() {
      this.state = State.unset(this.state, State.LOCKED)
    }
  },
  watch: {
    isHidden(isHidden) {
      if (isHidden) {
        this.hide()
      } else {
        this.show()
      }
    },
    isLocked(isLocked) {
      if (isLocked) {
        this.lock()
      } else {
        this.unlock()
      }
    },
    isValid(isValid) {
      if (isValid) {
        this.valid()
      } else {
        this.unvalid()
      }
    }
  },
  events: {
    reset() {
      this.reset()
    }
  }
}
</script>