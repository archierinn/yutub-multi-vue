<template>
  <v-container
    fill-height
    fluid
    class="ma-0 pa-0"
    style="max-height:100%;align-items:stretch !important"
  >
    <v-row
      v-if="links.length <= 16"
      no-gutters
    >
      <template v-for="(result, index) in links">
        <v-col
          :key="index"
          :cols="getCols(index)"
          class="ma-0 pa-0"
        >
          <v-card
            outlined
            tile
            height="100%"
            class="text-center ma-0 pa-0"
          >
            <h3 class="titles mt-4 ml-3">
              {{ result.title }}
            </h3>
            <v-fab-transition>
              <v-btn
                color="info"
                dark
                absolute
                fab
                small
                class="mt-3 mr-3"
                style="top:0px; right:0px"
                @click="rightDrawer = !rightDrawer"
              >
                <v-icon>mdi-comment</v-icon>
              </v-btn>
            </v-fab-transition>
            <iframe
              width="100%"
              height="100%"
              :src="result.link"
              frameborder="0"
              allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
              allowfullscreen
            />
          </v-card>
        </v-col>
        <!-- <v-responsive
          v-if="links.length % 5 < 2 && links.length % 2 > 0 && links.length > 1"
          :key="`width-${index}`"
          width="100%"
        /> -->
      </template>
    </v-row>
    <v-dialog
      v-model="dialog"
      max-width="50vw"
    >
      <template v-slot:activator="{ on }">
        <v-fab-transition>
          <v-btn
            color="primary"
            dark
            fab
            class="mb-3 mr-3"
            style="position:fixed;bottom:0px; right:0px"
            v-on="on"
          >
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </v-fab-transition>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">{{ formTitle }}</span>
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.title"
                  label="Title"
                  outlined
                />
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="editedItem.link"
                  label="Address"
                  outlined
                />
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn
            color="blue darken-1"
            text
            @click="close"
          >
            Cancel
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="save"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-navigation-drawer
      v-model="rightDrawer"
      right
      temporary
      fixed
      width="30%"
    >
      <v-card
        flat
        class="d-flex fill-height flex-column"
      >
        <v-card-title>
          Comment
        </v-card-title>
        <v-divider />
        <v-card-text class="flex-grow-1 overflow-y-auto">
          <v-list>
            <v-list-item
              v-for="(result, index) in comments"
              :key="index"
              class="px-0 mb-0"
              style="min-height:36px"
            >
              <v-list-item-content class="py-0">
                <v-list-item-title><b>{{ result.user }}</b> <span class="ml-3 text-subtitle-1">{{ result.message }}</span></v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </v-list>
        </v-card-text>
        <v-divider />
        <v-card-text class="flex-shrink-1">
          <v-text-field
            v-model="comment.message"
            label="Send comment"
            append-outer-icon="mdi-send"
            outlined
            solo
            flat
            hide-details
            @click:append-outer="send"
          />
        </v-card-text>
      </v-card>
    </v-navigation-drawer>
  </v-container>
</template>

<script>
export default {
  components: {
  },
  data () {
    return {
      formTitle: 'Add Link',
      dialog: false,
      rightDrawer: false,
      jumlah: 2,
      editedItem: {
        title: '',
        link: ''
      },
      comment: {
        message: '',
        created: null,
        user: ''
      },
      defaultItem: {
        title: '',
        link: ''
      },
      defaultComment: {
        message: '',
        created: null,
        user: ''
      },
      editedIndex: -1,
      links: [{
        title: 'Cam 001',
        link: 'https://www.youtube.com/embed/x8VYWazR5mE?autoplay=1'
      }
      /* {
        title: 'Cam 002',
        link: 'https://www.youtube.com/embed/sAuEeM_6zpk?autoplay=1'
      } */],
      comments: [{
        message: 'Ahoy',
        created: new Date().toISOString().substr(0, 10) + 'T' + new Date().toISOString().substr(11, 8),
        user: 'abc'
      }]
      // links: ['https://www.youtube.com/embed/x8VYWazR5mE?autoplay=1', 'https://www.youtube.com/embed/sAuEeM_6zpk?autoplay=1', 'https://www.youtube.com/embed/kzdJkT4kp-A?autoplay=1', 'https://www.youtube.com/embed/8iuLXODzL04?autoplay=1', 'https://www.youtube.com/embed/eKoD2CRr_KA?autoplay=1', 'https://www.youtube.com/embed/wsKSUGDKRpQ?autoplay=1', 'https://www.youtube.com/embed/vHIbNZza5As?autoplay=1', 'https://www.youtube.com/embed/hnLHUK1vCbA?autoplay=1', 'https://www.youtube.com/embed/kMJyIFmMKTM?autoplay=1']
    }
  },
  methods: {
    close () {
      this.dialog = false
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      }, 300)
    },

    save () {
      this.links.push(this.editedItem)
      this.close()
    },
    send () {
      const comment = { ...this.comment, user: 'abc', created: new Date().toISOString().substr(0, 10) + 'T' + new Date().toISOString().substr(11, 8) }
      this.comments.push(comment)
      this.comment = Object.assign({}, this.defaultComment)
    },
    getCols (index) {
      let col = 12
      if (this.links.length === 1) {
      } else if (this.links.length > 1 && this.links.length < 10) {
        if (this.links.length % 4 === 0 && this.links.length / 4 > 1) {
          col = col / 4
        } else if (this.links.length % 3 === 0 && this.links.length > 3) {
          col = col / 3
        } else if (this.links.length % 2 === 0) {
          col = col / 2
        } else {
          if (index >= this.links.length - (Math.floor(this.links.length / 2))) {
            col = col / (Math.floor(this.links.length / 2))
          } else {
            col = col / (Math.ceil(this.links.length / 2))
          }
          console.log(col)
        }
      } else if (this.links.length % 4 === 0) {
        col = col / 4
      } else if (this.links.length % 3 === 0) {
        if (index >= this.links.length - 3) {
          col = col / 3
        } else {
          col = col / 4
        }
      } else if (this.links.length % 2 === 0) {
        if (index >= this.links.length - 2) {
          col = col / 2
        } else {
          col = col / 4
        }
      } else {
        if (index >= (4 * Math.ceil((this.links.length - 8) / 2))) {
          col = col / (this.links.length - (4 * Math.ceil((this.links.length - 8) / 2)))
        } else {
          col = col / 4
        }
        console.log(col)
      }
      return col
    }
  }
}
</script>

<style scoped>
.titles {
  position: absolute;
}
</style>
