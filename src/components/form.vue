<template>
    <div class="form-contact" :class="{'is-active': formIsOpen}">
        <div class="form-button" v-show="!formIsOpen" @click="openForm">{{ formButtonMsg }}</div>
        <form class="form" @submit.prevent="processForm" @keydown.enter.prevent="">

            <div class="form__item">
                <label>Full name</label>
                <input type="text" name="name" class="input-item" v-model="formData.name" ref="name" v-validate="'required|alpha_spaces'" data-vv-delay="700" placeholder="Christoffer Berg">
                <p v-show="errors.has('name')" class="input-error">{{ errors.first('name') }}</p>
            </div>

            <div class="form__item">
                <label>Email</label>
                <input type="email" name="email" class="input-item" v-model="formData.email" v-validate="'required|email'" data-vv-delay="700" placeholder="eksempel@email.com">
                <!-- If it has an email error, display the first message associated with it. -->
                <p v-show="errors.has('email')" class="input-error">{{ errors.first('email') }}</p>
            </div>

            <div class="form__item">
                <label>Date of birth</label>
                <input type="text" name="date of birth" class="input-item" v-model="formData.dateOfBirth" v-validate="'required|date_format:DD-MM-YYYY'" placeholder="dd-mm-yyyy" data-vv-delay="700">
                <p v-show="errors.has('date of birth')" class="input-error">{{ errors.first('date of birth') }}</p>
            </div>

            <div class="form__item">
                <label>Favorite brands <span class="input-notice">– Add up to 5 different brands</span></label>

                <div class="inline">
                    <input type="text" class="input-item" ref="newBrand" v-model="newBrand" @keyup.enter="addBrand" placeholder="e.g. Audi">
                    <button type="button" class="btn btn--add" @click="addBrand">{{ btnAdd }}</button>
                </div>
            </div>

            <div class="brands" v-if="formData.favoriteBrands.length">
                <ul>
                    <li v-for="brand in formData.favoriteBrands">
                        {{ brand }}
                        <span class="delete" @click="removeBrand(brand)">Remove</span>
                    </li>
                </ul>

                <p v-if="formBrandMaxiumReached" class="input-notice">
                    {{ formBrandErrorMsg }}
                </p>
            </div>

            <div class="form__item">
                <label>Please choose any of the four categories</label>
                <div class="categories">
                    <div class="categories__item" v-for="item in categories">
                        <label>
                            <input type="checkbox" :value="item" v-model="formData.checkedCategories">
                            {{ item }}
                        </label>
                    </div>
                </div>
            </div>

            <div class="form__item">
                <label>Your message</label>
                <textarea class="input-item textarea" v-model="formData.message" placeholder="Hello, I would like to talk about...">
        </textarea>
            </div>

            <div class="form__actions">
                <button type="submit" class="btn btn--submit">{{ btnSubmit }}</button>
                <button type="reset" class="btn btn--cancel" @click="resetForm">{{ btnCancel }}</button>
            </div>

            <transition name="fade">
                <div class="result">
                    <p v-if="showSubmitMsg">{{ formSubmitMsg }}</p>
                    <p v-if="showErrorMsg">{{ formErrorMsg }}</p>
                </div>
            </transition>
        </form>
    </div>
</template>

<script>
    export default {
        name: "form",
        data() {
            return {
                formIsOpen: false,
                showSubmitMsg: false,
                showErrorMsg: false,
                formButtonMsg: "Click here to contact us!",
                formSubmitMsg: "Thank you for contacting us!",
                formErrorMsg: "Please correct your errors.",
                formBrandErrorMsg: "You have reached the maximum amount.",
                btnSubmit: "Send",
                btnCancel: "Cancel",
                btnAdd: "Add",
                newBrand: "",
                categories: ["Science", "Advertisement", "Politics", "Events"],
                formData: {
                    name: "",
                    email: "",
                    dateOfBirth: "",
                    message: "",
                    checkedCategories: [],
                    favoriteBrands: [],
                }
            }
        },
        computed: {
              formBrandMaxiumReached() {
                  return this.formData.favoriteBrands.length == 5 ? true : false;
              }
        },
        methods: {
            openForm() {
                this.formIsOpen = true;
                this.$refs.name.focus();
            },
            addBrand() {
                // trim() is used to remove whitespace from both ends of a string
                let newBrand = this.newBrand.trim();
                // if task is not an empty string
                if (newBrand && this.formData.favoriteBrands.length < 5) {
                    // Push entered brand to the favoriteBrands array
                    this.formData.favoriteBrands.push(newBrand);
                    // Reset newTask to an empty string so the input field is cleared
                    this.newBrand = "";
                    this.$refs.newBrand.focus();
                }
            },
            removeBrand(brand) {
                let index = this.formData.favoriteBrands.indexOf(brand);
                this.formData.favoriteBrands.splice(index, 1);
            },
            resetForm() {
                this.formData = {
                    name: "",
                    email: "",
                    message: "",
                    dateOfBirth: "",
                    checkedCategory: [],
                    favoriteBrands: [],
                };
                this.showSubmitMsg = false;
                this.showErrorMsg = false;
                this.formIsOpen = false;

                setTimeout(() => {
                    this.errors.clear();
                }, 710);
            },
            processForm() {
                this.$validator.validateAll().then((result) => {
                    if (result) {

                        console.log(this.formData);
                        this.showSubmitMsg = true;

                        setTimeout(() => {
                            this.resetForm();
                        }, 3000);

                        return;
                    }

                    this.showErrorMsg = true;
                });
            }
        },
    }
</script>

<style lang="scss">
    @import "~styles/settings";

    .form-contact {
        @include transition();
        cursor: pointer;
        height: auto;
        width: auto;
        margin: 20px;

        &.is-active {
            background-color: #fafafa;
            padding: 30px;
            border-radius: $form-border-radius;
            width: auto;
            cursor: default;
            @include card-2;

            form {
                opacity: 1;
                @include transition(.1s);
                transition-delay: 0.3s;
                height: auto;
                width: 100%;

                @include media(sm) {
                    width: 500px;
                }
            }
        }

        .form-button {
            text-align: center;
            text-transform: uppercase;
            font-weight: bold;
            padding: 30px;
            border-radius: $form-border-radius;
            background-color: $color-white;
            color: $color-font;
            @include card-1;
            @include transition();
            user-select: none;

            @include hover-state {
                @include card-2;
            };
        }

        .form {
            opacity: 0;
            height: 0;
            overflow: hidden;

            &__item {

                &:not(:last-child) {
                    margin-bottom: 20px;
                }

                label {
                    display: block;
                    font-size: 13px;
                    margin-bottom: 4px;
                    margin-left: 1px;
                    font-weight: bold;
                    user-select: none;
                }

                .inline {
                    display: flex;
                    flex-flow: row nowrap;
                    align-items: center;
                }

                .categories {
                    margin-top: $gutter-small;

                    &__item {
                        display: flex;
                        flex-flow: row wrap;
                        align-items: center;
                        line-height: 1;
                        margin: 0 5px;

                        &:not(:last-child) {
                            margin-bottom: 5px;
                        }
                    }
                }
            }

            &__actions {
                display: flex;
                flex-flow: row wrap;
                align-items: center;
                justify-content: center;
                margin: $gutter 0 0 0;
            }

            .brands {
                margin-bottom: $gutter;

                ul {
                    padding: 0;

                    li {
                        display: flex;
                        flex-flow: row wrap;
                        align-items: center;
                        line-height: 1;
                        background-color: $color-white;
                        padding: 7px 5px 7px 10px;
                        font-size: 13px;

                        &:not(:last-child) {
                            margin-bottom: 5px;
                        }

                        @include hover-state {
                            span {
                                opacity: 1;
                            }
                        };

                        span {
                            margin-left: auto;
                            text-transform: uppercase;
                            font-weight: bold;
                            font-size: 10px;
                            color: $color-grey;
                            @include transition();
                            cursor: pointer;
                            padding: 5px;

                            @include media(sm) {
                                opacity: 0;
                            }
                        }
                    }
                }
            }

            .input-item {
                @include input-base;

                &.textarea {
                    height: 120px;
                    resize: none;
                }
            }

            .result {
                text-align: center;
                margin-top: $gutter;
            }
        }
    }

</style>
