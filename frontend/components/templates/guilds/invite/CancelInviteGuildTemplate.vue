<template>
  <div>
    <Title text="Cancel Invites" />

    <v-container>
      <div v-if="isLoading" class="text-center">
        <Loading style="width: 50% !important;" />
      </div>

      <ValueNoExists 
        v-else-if="guildInvalid"
        text="Guild not found"
        to="/guilds"
      />

      <CancelInvite v-else/>
    </v-container>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { guild } from '@/store'

export default Vue.extend({
  data() {
    return {
      isLoading: true,
      guildInvalid: false
    }
  },

  computed: {
    getGuildName(): string | void {
      if (this.$route.params && this.$route.params.name)
        return this.$route.params.name;
    },
  },
  
  async mounted(this: any) {
    await this.getGuild();
  },

  methods: {
    async getGuild(this: any) {
      try {
        this.$set(this, 'isLoading', true);

        const res = await guild.getGuild(this.getGuildName);

        if (res !== 200) {
          this.$set(this, 'guildInvalid', true);
        }
        else {
          this.$set(this, 'guildInvalid', false);
          await guild.getInvites({
            guild_id: guild.$guild.info.id
          });
        }
        this.$set(this, 'isLoading', false);
      } catch(err) {
        console.log(err);
      }
    },
  }
})
</script>