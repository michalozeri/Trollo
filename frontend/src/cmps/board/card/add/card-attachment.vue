<template>
  <div class="card-attachment">
    <div class="spinner" v-if="isLoading">
      <img src="../../../../assets/imgs/spinner.gif" alt="spinner"/>
    </div>
    <main v-else>
      <form @submit.prevent="attach()">
        <div class="upload">
          <label>
          Computer
          <input hidden class="file" type="file" id="upload" @change="onUpload($event)" />
        </label>
      </div>
      <label>
        Attach a link
        <input class="input" v-model="attachment.url" type="url" placeholder="Paste any link here..." @change="attachment.type = 'link'" />
      </label>
      <label>
        Attachment name (optional)
        <input class="input" type="text" v-model="attachment.title" />
      </label>
      <button class="btn-action-list">Attach</button>
    </form>
    </main>
    
  </div>
</template>

<script>
import { utilService } from "@/services/util.service.js";
export default {
  name: "card-attachment",
  props: ['card'],
  data() {
    return {
      attachment: {
        type: 'link',
        url: '',
        title: 'Attachment',
        createdAt: Date.now(),
        isEdit: false,
      },
      upload: null,
      isLoading: false,
    };
  },
  methods: {
    cardCopy() {
      return JSON.parse(JSON.stringify(this.card));
    },
    attach(){
      const id = utilService.makeId();
      let tmpCard = this.cardCopy();
      if (this.upload) {
        tmpCard.attachments.unshift({ ...this.upload, id, title: this.attachment.title});
        tmpCard.style = { color: this.upload.url, size: 'header' };
      } 
      else{
        tmpCard.attachments.unshift({...this.attachment, id});
      }
      this.$emit("updateCard", tmpCard);
      this.$emit("closeActionModal", "attachment");
    },
    async onUpload(ev) {
      this.isLoading = true;
      const res = await utilService.upload(ev);
      this.upload = {type: res.type, url: res.url, createdAt: Date.now()}
      this.$notify({ title: "Upload finished successfully", type: "success" });
      this.isLoading = false;
    },
  },
  computed: {
    board() {
      return this.$store.getters.board || null;
    },
    groupId() {
      return this.$route.params.groupId;
    },
    // card() {
    //   return this.$store.getters.currCard;
    // },
  },
};
</script>
	<!-- <div class="card-attachment">
		<form @submit.prevent="onUpload($event)">
			<label name="link">
				Attach a link
				<input v-model="attachment.url" type="url" placeholder="Pase any link here..." @change="attachment.type = 'link'" />
			</label>
			<label name="media">
				Upload image or video from computer
				<input type="file" id="upload" @change="getUploadUrl($event)" />
			</label>
			<label name="name">
				Attachment name (optional)
				<input type="text" v-model="attachment.title" />
			</label>
			<el-button type="primary" @click="onUpload(event)">Attach</el-button>
			<el-button type="info" title="Discard" @click="discardChanges()">Discard</el-button>
		</form>
	</div> -->
</template>

<!-- // <script>
// 	import {utilService} from '@/services/util.service.js'
// 	export default {
// 		name: 'card-attachment',

// 		data() {
// 			return {
// 				attachment: {
// 					type: '',
// 					url: '',
// 					title: 'Attachment',
// 					createdAt: Date.now(),
// 					isEdit: false,
// 				},
// 				isLoading: false,
// 			}
// 		},
// 		methods: {
// 			cardCopy() {
// 				return JSON.parse(JSON.stringify(this.card))
// 			},
// 			async onUpload(ev,) {
// 				let tmpCard = this.cardCopy()
// 				this.attachment.id = utilService.makeId();
// 				if (this.attachment.type === 'link') {
// 					tmpCard.attachments.push({...this.attachment, id:utilService.makeId()})
// 				} else {
// 					const res = await utilService.upload(ev, this.attachment.url)
// 					tmpCard.attachments.push({...this.attachment, id:utilService.makeId(), type: res.type, url: res.url})
// 					this.$notify({title: 'Finished!', type: 'success'})
// 				}
// 				this.$emit('updateCard', tmpCard)
// 				// await this.$store.dispatch({type: 'saveCard', payload: {groupId: this.groupId, card: tmpCard}})
// 			},
// 			discardChanges() {
// 				this.$emit('closeActionModal', 'attachment')
// 			},
// 			getUploadUrl(ev){
// 				this.attachment.url = URL.createObjectURL(ev.target.files[0])
// 			}
// 		},
// 		computed: {
// 			board() {
// 				return this.$store.getters.board || null
// 			},
// 			groupId() {
// 				return this.$route.params.groupId
// 			},
// 			card() {
// 				return this.$store.getters.currCard
// 			},
// 		},
// 	}
// </script> -->
