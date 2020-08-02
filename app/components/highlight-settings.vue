<template>

    <div class="uk-form uk-form-horizontal">
        <h1>{{ 'Highlight Settings' | trans }}</h1>
        <div class="uk-form-row">
            <label class="uk-form-label" for="input-style">{{ 'Style' | trans }}</label>
            <div class="uk-form-controls">
                <select v-show="styles.length" v-model="package.config.style" id="input-style">
                    <option v-for="(option , id) in styles" :key="id" :value="option">
                        {{ option }}
                    </option>
                </select>
                <div v-show="!styles.length">
                    {{ 'Loading styles...' | trans }}
                </div>
            </div>
        </div>

        <div class="uk-form-row">
            <label class="uk-form-label">{{ 'Pages' | trans }}</label>
            <div class="uk-form-controls uk-form-controls-text">
                <input-tree :active.sync="package.config.nodes"></input-tree>
            </div>
        </div>

        <div class="uk-form-row">
            <label class="uk-form-label" for="input-enable-auto">{{ 'Auto Detect' | trans }}</label>
            <div class="uk-form-controls uk-form-controls-text">
                <input v-model="package.config.autodetect" type="checkbox" id="input-enable-auto" name="input-enable-auto" value="auto">
                <label for="input-enable-auto">
                    {{ 'Only load when code found on page' | trans }}
                </label>
            </div>
        </div>

        <div class="uk-form-row uk-margin-top">
            <div class="uk-form-controls">
                <button class="uk-button uk-button-primary" @click="save">{{ 'Save' | trans }}</button>
            </div>
        </div>

    </div>

</template>

<script>

    let highlight = {
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
                    this.$set('styles', response.data.styles);
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

    export default highlight
    window.Extensions.components['highlight-settings'] = highlight;
</script>
