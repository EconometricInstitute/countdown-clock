<template>
    <v-container style="max-width: 50em">
        <v-row>
            <v-col>
                <v-card>
                    <v-card-title>Deadline Countdown Clock</v-card-title>
                    <v-card-text>No deadline was detected. Choose a deadline, and generate either a link to a countdown or an iframe you can paste into a Canvas assignment or page.</v-card-text>
                </v-card>
            </v-col>
        </v-row>
        <v-row>
            <v-col>
                <v-card>
                    <v-card-title>Step 1: Pick a deadline</v-card-title>
                    <v-card-text>
                        <v-text-field style="max-width: 14em;" type="datetime-local" v-model="deadline" label="Choose a deadline" />
                    </v-card-text>
                </v-card>
            </v-col>         
        </v-row>
        <v-row v-if="deadline">
            <v-col>
                <v-card>
                    <v-card-title>Step 2: Get link or iframe</v-card-title>
                    <v-card-text>
                        Deadline set to {{ deadline }}.
                        <br />
                        Use the buttons below to export this deadline as a link or as
                        iframe code that can be pasted into Canvas or some other application.
                        <br />
                        <v-select
                            v-model="currentDims"
                            :items="dimensions"
                            label="Choose a size for the iframe"
                            item-title="label"
                            persistent-hint
                            return-object
                            single-line
                        />
                    </v-card-text>
                    <v-card-actions>
                        <v-btn color="primary" target="_blank" :href="link">Open preview</v-btn>
                        <v-btn color="primary" @click="copyLink">Copy link</v-btn>
                        <v-btn color="primary" :disabled="!currentDims" @click="copyIframe">Copy iframe</v-btn>
                    </v-card-actions>
                </v-card>
            </v-col>         
        </v-row>
        <v-snackbar centered v-model="snackbar" :timeout="3000">
        {{ snackbarText }}
            <template v-slot:action="{ attrs }">
                <v-btn
                color="primary"
                text
                v-bind="attrs"
                @click="snackbar = false"
                >
                Close
                </v-btn>
            </template>
        </v-snackbar>        
    </v-container>

  </template>
  
  <script lang="ts">
    import { defineComponent } from 'vue';
    import copyToClipboard from 'copy-to-clipboard';

    import CountdownClock from '@/components/CountdownClock.vue'
  
    export default defineComponent({
        components: {
            CountdownClock
        },
        data: () => ({
            deadline: null,
            snackbarText: '',
            snackbar: false,
            dimensions: [
                { label: 'Small', dims: { width: '250px', height:'150px' }},
                { label: 'Medium', dims: { width: '500px', height: '200px' }},
                { label: 'Large', dims: { width: '800px', height: '300px' }},
            ],
            currentDims: null
        }),
        methods: {
            iframeCode(dims) {
                return `<p><iframe title="Countdown Clock" width="${dims.width}" height="${dims.height}" src="${this.link}"></iframe></p>`;
            },
            copyLink() {
                copyToClipboard(this.link);
                this.snackbarText = 'Link copied to the clipboard.';
                this.snackbar = true;
            },
            copyIframe() {
                const text = this.iframeCode(this.currentDims.dims);
                copyToClipboard(text, { format: 'text/html' });
                this.snackbarText = 'Code for iframe copied to the clipboard.';
                this.snackbar = true;                
            }
        },
        computed: {
            transformedDeadline() {
                if (this.deadline) {
                    const date: Date = new Date(this.deadline);
                    return date.getTime();
                }
            },
            link() {
                const location = window.location;
                return `${location.protocol}//${location.host}${location.pathname}?deadline=${this.transformedDeadline}`;
            },
        },
    });
  </script>
  