<template>
  <div class="notification option">
    <author class="author"
            v-if="notificationMeta.user"
            :user="notificationMeta.user"
            :created-at="notificationMeta.createdAt" />
    <p class="notification-message" v-html="message"></p>
  </div>
</template>

<script>
  import author from '~/components/Author/Author.vue'
  import { isEmpty } from 'lodash'

  export default {
    name: 'hc-notification-item',
    components: {
      'author': author
    },
    props: {
      notification: {
        type: Object,
        required: true
      }
    },
    computed: {
      notificationMeta () {
        const metaExists = !isEmpty(this.notification[this.type])
        const meta = metaExists ? this.notification[this.type] : this.notification
        return {
          user: meta.user || false,
          createdAt: meta.createdAt || '',
          title: meta.title || ''
        }
      },
      userName () {
        return this.notificationMeta.user ? this.notificationMeta.user.name : 'Anonymus'
      },
      type () {
        return this.notification.type || 'comment'
      },
      message () {
        return this.notification.message || this.$t(`component.notification.message.${this.type}`, this.messageParams)
      },
      messageParams () {
        return {
          userName: this.userName,
          title: !isEmpty(this.notification.contribution) ? this.notification.contribution.title : ''
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import "assets/styles/utilities";

  .author {
    pointer-events: none;
  }

  .option {
    border-bottom: 1px solid lighten($grey-lighter, 6%);
    transition: all .2s ease-out;
    padding: 0.5rem 1rem;
    margin-bottom: 0;
    cursor: pointer;
    background-color: $white;

    &:hover {
      // box-shadow: 0px 2px 7px rgba($black, 0.2);
      background-color: lighten($grey-lighter, 10%);
    }

    &:last-of-type {
      border-bottom: 0;
    }
  }

  .notification-message {
    // margin-top: 0.5em;
    padding: 1rem 0 0.5rem;
  }


  .notification {
    p {
      font-size: $size-7;
    }
  }
</style>ing: 1rem 0 0.5rem;
  }


  .notification {
    p {
      font-size: $size-7;
    }
  }
</style>
