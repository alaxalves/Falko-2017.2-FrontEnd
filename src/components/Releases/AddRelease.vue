<template>
  <div>
    <div align="center">
      <button type="button" class="btn btn-info btn-md falko-button" id="addReleaseButton" data-toggle="modal" data-target="#addReleaseModal" align="center">
        Add Release
      </button>
    </div>

    <div class="modal fade" id ="addReleaseModal" role="dialog">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title">Add Release</h4>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="row modal-body align-content-end">
            <div class="col">
              <p><label>Name</label></p>
              <p><input type="text" v-model="name" placeholder="Release name..." id="releaseName"></input><br></p>
              <p><label>Description</label></p>
              <input type="text" v-model="description" placeholder="Release description..."></input><br>
            </div>
            <div class="col">
              <p><label>Initial Date</label></p>
              <p><input type="date" v-model="initialDate"></input><br></p>
              <p><label>Final Date</label></p>
              <p><input type="date" v-model="finalDate" v-bind:min="this.initialDate"></input><br></p>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-info btn-md falko-button" v-on:click="addRelease()" data-dismiss="modal">Save</button>
            <button type="button" class="btn btn-info btn-md falko-button-grey" data-dismiss="modal" >Close</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex';
import { EventBus } from '../../event-bus';
import { HTTP } from '../../http-common';

export default {
  data() {
    return {
      name: '',
      description: '',
      initialDate: '',
      finalDate: '',
      amount_of_sprints: 0,
    };
  },
  computed: {
    ...mapState({
      token: state => state.auth.token,
      projectId: state => state.clientStatus.projectId,
    }),
  },
  methods: {
    addRelease() {
      const headers = { Authorization: this.token };

      HTTP.post(`/projects/${this.projectId}/releases`, {
        release: {
          name: this.name,
          description: this.description,
          initial_date: this.initialDate,
          final_date: this.finalDate,
          amount_of_sprints: this.amount_of_sprints,
        },
      }, { headers })
        .then(() => {
          this.name = '';
          this.description = '';
          this.initialDate = '';
          this.finalDate = '';

          EventBus.$emit('added-release');
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },
  },

};
</script>

<style scoped>
#releaseName {
  color: #777;
}
#addReleaseButton {
  width: 100%;
  border-radius: 0;
  padding: 0.9em;
  margin: 0;
  background-color: #637074;
}

#addReleaseButton:hover {
  background-color: #4a575b;
}

.modal-body{
  position: relative;
  top: 5px;
}

p {
  margin-bottom: 0.5em;
}

label {
  margin-bottom: 0em;
}
</style>
