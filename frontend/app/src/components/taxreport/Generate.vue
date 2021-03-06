<template>
  <v-form v-model="valid">
    <v-card>
      <v-card-title v-text="$t('generate.title')" />
      <v-card-text>
        <v-row>
          <v-col cols="12">
            <date-time-picker
              v-model="start"
              label="Start Date"
              limit-now
              :rules="startRules"
            />
          </v-col>
        </v-row>
        <v-row>
          <v-col cols="12">
            <date-time-picker
              v-model="end"
              label="End Date"
              limit-now
              :rules="endRules"
            />
          </v-col>
        </v-row>
        <v-alert v-model="invalidRange" type="error">
          {{ message }}
        </v-alert>
      </v-card-text>
      <v-card-actions>
        <v-btn
          color="primary"
          depressed
          :disabled="!valid || invalidRange"
          @click="generate()"
          v-text="$t('generate.generate')"
        />
      </v-card-actions>
    </v-card>
  </v-form>
</template>

<script lang="ts">
import moment from 'moment';
import { Component, Vue, Watch } from 'vue-property-decorator';
import DateTimePicker from '@/components/dialogs/DateTimePicker.vue';

@Component({
  components: {
    DateTimePicker
  }
})
export default class Generate extends Vue {
  start: string = '';
  end: string = '';
  valid: boolean = false;
  invalidRange: boolean = false;
  message: string = '';

  startRules: ((v: string) => boolean | string)[] = [
    (v: string) =>
      !!v || this.$t('generate.validation.empty_start_date').toString()
  ];

  endRules: ((v: string) => boolean | string)[] = [
    (v: string) =>
      !!v || this.$t('generate.validation.empty_end_date').toString()
  ];

  private convertToTimestamp(date: string): number {
    const format = date.indexOf(' ') > -1 ? 'DD/MM/YYYY HH:mm' : 'DD/MM/YYYY';
    return moment(date, format).unix();
  }

  @Watch('start')
  onStartChange() {
    this.invalidRange =
      !!this.start &&
      !!this.end &&
      this.convertToTimestamp(this.start) > this.convertToTimestamp(this.end);
    this.message = this.$t('generate.validation.end_after_start').toString();
  }

  @Watch('end')
  onEndChange() {
    this.invalidRange =
      !!this.start &&
      !!this.end &&
      this.convertToTimestamp(this.start) > this.convertToTimestamp(this.end);
    this.message = this.$t('generate.validation.end_after_start').toString();
  }

  generate() {
    const start = this.convertToTimestamp(this.start);
    const end = this.convertToTimestamp(this.end);
    this.$emit('generate', {
      start,
      end
    });
  }
}
</script>

<style scoped></style>
