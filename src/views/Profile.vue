<template>
  <div id="container">
    <div class="sidebar">
      <ProfileImage
        class="profile-img"
        :profile-image-u-r-l="$store.getters.getUser.ProfileImageURL "
        editable
      />

      <div class="userPersonal">
        <h3>{{ $store.getters.getUser.FirstName }} {{ $store.getters.getUser.LastName }}</h3>
        <h5>{{ $store.getters.getUser.Email }}</h5>
        <br>
      </div>
      <div class="tab">
        <BlockButton
          :click="() => currentTab='profile'"
          :color="currentTab==='profile'?'purple-light':'gray-light'"
          class="btn tabItems"
        >
          Edit Profile
        </BlockButton>
        <BlockButton
          :click="() => currentTab='updatePass'"
          :color="currentTab==='updatePass'?'purple-light':'gray-light'"
          class="btn tabItems"
        >
          Security
        </BlockButton>
      </div>
    </div>
    <div class="profileContent">
      <div
        v-if="currentTab === 'profile'"
        class="visualItems"
      >
        <h3>Edit Your Profile</h3>
        <hr>
        <div class="innerProfile">
          <form @submit.prevent="updateUser">
            <div class="row">
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.handle"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.Handle"
                    type="text"
                  />
                  <label>Qvault Handle</label>
                </span>
              </div>
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.firstName"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.FirstName"
                    type="text"
                  />
                  <label>First Name</label>
                </span>
              </div>
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.lastName"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.LastName"
                    type="text"
                  />
                  <label>Last Name</label>
                </span>
              </div>
            </div>

            <div class="row">
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.jobTitle"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.JobTitle"
                    type="text"
                  />
                  <label>Job Title</label>
                </span>
              </div>
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.location"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.Location"
                    type="text"
                  />
                  <label>Location</label>
                </span>
              </div>
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.websiteURL"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.WebsiteURL"
                    type="text"
                  />
                  <label>Website</label>
                </span>
              </div>
            </div>

            <div class="row">
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.twitterHandle"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.TwitterHandle"
                    type="text"
                  />
                  <label>Twitter Handle</label>
                </span>
              </div>
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.linkedinURL"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.LinkedinURL"
                    type="text"
                  />
                  <label>Linkedin URL</label>
                </span>
              </div>
              <div class="col">
                <span>
                  <TextInput
                    v-model="user.githubHandle"
                    class="inputItems"
                    :placeholder="$store.getters.getUser.GithubHandle"
                    type="text"
                  />
                  <label>Github Username</label>
                </span>
              </div>
            </div>

            <div class="row">
              <div class="col">
                <textarea
                  v-model="user.bio"
                  :placeholder="$store.getters.getUser.Bio"
                  class="inputItems textarea"
                  rows="4"
                />
                <label>Bio</label>
              </div>
            </div>
            <div class="text-center">
              <BlockButton class="btn">
                Update
              </BlockButton>
            </div>
          </form>
        </div>
      </div>

      <div
        v-if="currentTab === 'updatePass'"
        class="visualItems"
      >
        <h3>Security Preferences</h3>
        <hr>

        <div class="profile-edit-box">
          <form
            id="form"
            @submit.prevent="updatePassword"
          >
            <p class="title">
              Change Password
            </p>
            <TextInput
              v-model="oldPassword"
              placeholder="Old Password"
              type="password"
              class="inputItems vertical"
            />
            <TextInput
              v-model="newPassword"
              class="inputItems vertical"
              placeholder="New Password"
              type="password"
            />
            <TextInput
              v-model="confirmPassword"
              class="inputItems vertical"
              placeholder="Confirm Password"
              type="password"
            />
            <BlockButton class="btn">
              Submit
            </BlockButton>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { 
  updateUserPassword, 
  updateUser, 
  updateUserHandle
} from '@/lib/cloudClient.js';
import { loadUser } from '@/lib/cloudStore.js';
import BlockButton from '@/components/BlockButton';
import ProfileImage from '@/components/ProfileImage';
import TextInput from '@/components/TextInput';

export default {
  components: {
    BlockButton,
    TextInput,
    ProfileImage
  },
  data() {
    return {
      currentTab: 'profile',
      oldPassword: null,
      newPassword: null,
      confirmPassword: null,
      user: {}
    };
  },
  methods: {
    async updateUser() {
      try {
        if (this.user.handle){
          await updateUserHandle(this.user.handle);
        }
        await updateUser(
          this.user.firstName,
          this.user.lastName,
          this.user.bio,
          this.user.jobTitle,
          this.user.location,
          this.user.twitterHandle,
          this.user.linkedinURL,
          this.user.githubHandle,
          this.user.websiteURL
        );
        await loadUser(this);
        this.$notify({
          type: 'success',
          text: 'Profile updated successfully'
        });
      } catch (err) {
        this.$notify({
          type: 'error',
          text: err
        });
      }
    },
    async updatePassword() {
      try {
        if (this.newPassword !== this.confirmPassword){
          this.$notify({
            type: 'error',
            text: 'Passwords don\'t match'
          });
          return;
        }
        await updateUserPassword(
          this.email,
          this.oldPassword,
          this.newPassword
        );
        this.$notify({
          type: 'success',
          text: 'Password changed successfully'
        });
        this.oldPassword = null;
        this.newPassword = null;
      } catch (err) {
        this.$notify({
          type: 'error',
          text: err
        });
      }
    }
  }
};
</script>

<style scoped lang="scss">
    @import "@/styles/colors.scss";

    label {
      font-size: .75em;
      color: $gold-dark;
    }

    .profile-img {
      width: 175px;
    }

    #container {
      color: $gray-darker;
      display: flex;
      flex-wrap: wrap;
      background-image: linear-gradient(45deg, rgb(58, 188, 117) 0%, rgb(58, 188, 117) 14%,rgb(61, 172, 114) 14%, rgb(61, 172, 114) 19%,rgb(65, 157, 111) 19%, rgb(65, 157, 111) 24%,rgb(68, 141, 108) 24%, rgb(68, 141, 108) 26%,rgb(72, 126, 104) 26%, rgb(72, 126, 104) 32%,rgb(75, 110, 101) 32%, rgb(75, 110, 101) 51%,rgb(79, 95, 98) 51%, rgb(79, 95, 98) 93%,rgb(82, 79, 95) 93%, rgb(82, 79, 95) 100%);
      height: 100%;

      .sidebar {
        width: 300px;
        text-align: center;
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;

        @media (max-width: 768px) {
          width: 100%;
        }

        .userPersonal {
          margin-top: 1em;
          color: $white;
          position: relative;

          h3, h5 {
            margin: 0;
          }
        }
      }

      .profileContent {
        flex: 1;
        background-image: radial-gradient(circle at center center, transparent,rgb(255,255,255)),linear-gradient(309deg, rgba(90, 90, 90,0.05) 0%, rgba(90, 90, 90,0.05) 50%,rgba(206, 206, 206,0.05) 50%, rgba(206, 206, 206,0.05) 100%),linear-gradient(39deg, rgba(13, 13, 13,0.05) 0%, rgba(13, 13, 13,0.05) 50%,rgba(189, 189, 189,0.05) 50%, rgba(189, 189, 189,0.05) 100%),linear-gradient(144deg, rgba(249, 249, 249,0.05) 0%, rgba(249, 249, 249,0.05) 50%,rgba(111, 111, 111,0.05) 50%, rgba(111, 111, 111,0.05) 100%),linear-gradient(166deg, rgba(231, 231, 231,0.05) 0%, rgba(231, 231, 231,0.05) 50%,rgba(220, 220, 220,0.05) 50%, rgba(220, 220, 220,0.05) 100%),linear-gradient(212deg, rgba(80, 80, 80,0.05) 0%, rgba(80, 80, 80,0.05) 50%,rgba(243, 243, 243,0.05) 50%, rgba(243, 243, 243,0.05) 100%),radial-gradient(circle at center center, hsl(107,19%,100%),hsl(107,19%,100%));
        padding: 15px;
        overflow: auto;
        background-repeat: no-repeat;
        background-size: cover;
        background-attachment: fixed;

        .visualItems {
          
          h3 {
            color: $purple-darker;
          }

          hr {
            border-color: $gray-lighter;
          }

          .inputItems {
            line-height: 26px;
            width: 100%;
            font-family: 'Roboto', sans-serif;

            &.textarea {
              outline: none;
              resize: none;
              border-color: $gray-lighter;

              &:focus{
                border-color: $purple-dark;
              }
            }

            &.vertical{
              margin-bottom: 1em;
            }
          }

          .profile-edit-box {
            position: relative;
            width: 70%;
            margin: 0 auto;
            box-shadow: 0 3px 9px 2px #0000001c;
            @media (max-width: 768px) {
              width: 99%;
            }

            form {
              display: flex;
              justify-content: center;
              flex-direction: column;
              padding: 30px;
              align-items: center;
              background: $white;
              margin: 35px 0;

              .title {
                text-align: center;
                font-size: 1.4rem;
                padding: 12px 27px;
                font-weight: bold;
                color: #5e62b5;
              }
            }
          }

          .btn {
            width: 200px;
            border-radius: 50px;
            padding: 10px;
            font-size: 1rem;
            margin: 10px;
          }
        }
      }
    }

    .innerProfile {
      width: 80%;
      margin: 33px auto;
      padding: 25px;
      background: $white;
      box-shadow: 0 3px 9px 2px $gray-lighter;

      .row {
        display: flex;
        flex-wrap: wrap;
        @media (max-width: 768px) {
          flex-direction: column;
        }

        .col {
          flex: 1;
          padding: 15px;
        }
      }
    }

    .tab {
      width: 75%;
      margin: 50px 0;
      display: flex;
      flex-direction: column;

      .tabItems {
        margin: 0 .5em 1em .5em;
        font-weight: bold;
      }
    }
</style>
