<template>
    <form class="uk-form-horizontal uk-margin-large" @submit.prevent="save">
        <div class="uk-modal-header">
            <h1 class="uk-h3">{{ 'Highlight Settings' | trans }}</h1>
        </div>
        <div class="uk-modal-body">
            <div class="uk-margin">
                <label class="uk-form-label uk-text-left">{{ 'Style' | trans }}</label>
                <div class="uk-form-controls">
                    <select v-show="styles.length" v-model="package.config.style" class="uk-select" id="input-style">
                        <option v-for="(option , id) in styles" :key="id" :value="option">
                            {{ option }}
                        </option>
                    </select>
                    <div v-show="!styles.length">
                        {{ 'Loading styles...' | trans }}
                    </div>
                </div>
            </div>

            <div class="uk-margin">
                <label class="uk-form-label uk-text-left">{{ 'Pages' | trans }}</label>
                <div class="uk-form-controls">
                    <input-tree :active.sync="package.config.nodes"></input-tree>
                </div>
            </div>

            <div class="uk-margin">
                <label class="uk-form-label uk-text-left" for="input-enable-auto">{{ 'Auto Detect' | trans }}</label>
                <div class="uk-form-controls uk-form-controls-text">
                    <input v-model="package.config.autodetect" type="checkbox" class="uk-checkbox" id="input-enable-auto" name="input-enable-auto" value="auto">
                    <label for="input-enable-auto">
                        {{ 'Only load when code found on page' | trans }}
                    </label>
                </div>
            </div>
        </div>
        <div class="uk-modal-footer uk-text-right">
            <button class="uk-button uk-button-primary" type="submit">{{ 'Save' | trans }}</button>
        </div>
    </form>
</template>

<script>

    module.exports = {
        name: 'highlight',

        settings: true,

        props: ['package'],

        data() {
            return {
                styles: []
            };
        },

        created() {
            this.load();
        },

        methods: {

            load() {
                this.$http.get('admin/highlight/config').then(function (response) {
                    this.$set(this, 'styles', response.body.styles);
                }).catch(function () {
                    this.$notify('Could not load styles.');
                });
            },

            save() {
               this.$http.post('admin/system/settings/config', {
                   name: 'highlight',
                   config: this.package.config
               }).then(function () {
                   this.$notify('Settings saved.', '');
               }).catch(function (response) {
                   this.$notify(response.message, 'danger');
               }).finally(function () {
                   this.$parent.close();
               });
           }
       }
    };

    window.Extensions.components['highlight-settings'] = module.exports;
</script>
