<template>
    <v-app>
        <v-main>
            <v-content>
                <h1>Choose an image file</h1>
                <v-file-input
                    @change="getBase64()"
                    v-model="fileInput"
                    truncate-length="30"
                ></v-file-input>
                <template v-if="base64">
                    <v-card elevation="0">
                        <v-card-text>
                            <v-textarea
                                label="base64"
                                :value="base64"
                                no-resize
                                rows="6"
                                counter
                            ></v-textarea>
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn
                                @click="copyTextToClipboard"
                            >
                                Copy
                            </v-btn>
                        </v-card-actions>
                    </v-card>


                    <v-card elevation="0">
                        <v-card-title>
                            Your picture
                        </v-card-title>
                        <v-card-text>
                            <img :src="base64"/>
                        </v-card-text>
                    </v-card>

                </template>

                <v-dialog
                    v-model="dialog"
                    max-width="80%"
                    width="400"
                >
                    <v-card>
                        <v-card-title>Error</v-card-title>
                        <v-card-text>
                            {{ errMsg }}
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn
                                color="primary"
                                text
                                @click="dialog = false"
                            >
                                Close
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                </v-dialog>
            </v-content>
        </v-main>
    </v-app>
</template>

<script>

export default {
    name: 'App',
    components: {},
    data: () => ({
        fileInput: {},
        base64: '',
        errMsg: '',
        dialog: false,
    }),
    methods: {
        async getBase64() {
            try {
                this.base64 = await new Promise((resolve, reject) => {
                    const reader = new FileReader();
                    reader.readAsDataURL(this.fileInput);
                    reader.onload = () => resolve(reader.result);
                    reader.onerror = error => reject(error);
                });
            } catch (e) {
                this.errMsg = e.message;
            }
        },
        async copyTextToClipboard() {
            if (!navigator.clipboard) {
                this.errMsg = 'Your browser does not support this function.';
                this.dialog = true;
                return;
            }

            try {
                await navigator.clipboard.writeText(this.base64);
            } catch (e) {
                this.errMsg = 'Could not copy text: ' + e.message;
            }
        },
    },
};
</script>
