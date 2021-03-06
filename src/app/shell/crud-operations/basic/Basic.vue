<template>
  <v-container id="basic" class="shell-container" fluid>
    <v-layout column>
      <div class="headline">CRUD Operations - Basic</div>

      <!-- Add -->
      <v-layout row>
        <div class="o-text-field">
          <v-text-field v-model="b$.addData.primary" name="primary" label="Primary"></v-text-field>
        </div>
        <div class="o-text-field">
          <v-text-field v-model="b$.addData.accent" name="accent" label="Accent"></v-text-field>
        </div>
        <v-btn color="primary" @click="addItem(b$.addData)">Add</v-btn>
      </v-layout>

      <!-- Display -->
      <v-layout row>
        <v-card>
          <template v-if="b$.selected.length !== 0">
            <v-card-title class="o-card-title">
              <div class="body-2 error--text">
                {{ b$.selected.length }} selected
              </div>
              <v-spacer></v-spacer>
              <!-- Delete checked -->
              <v-btn icon class="mx-0" @click.stop="deleteChecked(b$.selected)">
                <v-icon color="error">delete</v-icon>
              </v-btn>
            </v-card-title>
          </template>

          <template v-else>
            <v-card-title class="o-card-title">
              <div class="title">Board</div>
              <v-spacer></v-spacer>
              <!-- Search -->
              <v-text-field v-model="b$.searchData" append-icon="search" label="Search" single-line hide-details></v-text-field>
            </v-card-title>
          </template>

          <v-data-table :headers="b$.headers" :items="b$.dataset" :search="b$.searchData" v-model="b$.selected" hide-actions select-all>
            <template slot="items" slot-scope="props">
              <td>
                <v-checkbox v-model="props.selected" primary hide-details></v-checkbox>
              </td>
              <td>{{ props.item.id }}</td>
              <td>{{ props.item.primary }}</td>
              <td>{{ props.item.accent }}</td>
              <td class="text-xs-right">
                <v-btn icon class="mx-0" @click.stop="handleDialog({ name: 'edit', value: true }); dialogData({ item: props.item, key: 'editData' })">
                  <v-icon color="info">edit</v-icon>
                </v-btn>
                <v-btn icon class="mx-0" @click.stop="handleDialog({ name: 'delete', value: true }); dialogData({ item: props.item, key: 'deleteData' })">
                  <v-icon color="error">delete</v-icon>
                </v-btn>
              </td>
            </template>
          </v-data-table>
        </v-card>
      </v-layout>

      <!-- dialogs -->
      <aside>
        <!-- Edit -->
        <v-dialog v-model="b$.dialogs.edit" max-width="500px">
          <v-card>
            <v-card-title>Edit</v-card-title>
            <v-card-text>
              <v-layout v-if="b$.editData" row>
                <div class="o-text-field">
                  <v-text-field v-model="b$.editData.primary" name="primary" label="Primary"></v-text-field>
                </div>
                <div class="o-text-field">
                  <v-text-field v-model="b$.editData.accent" name="accent" label="Accent"></v-text-field>
                </div>
              </v-layout>
            </v-card-text>
            <v-card-actions>
              <v-btn color="error" flat @click.stop="handleDialog({ name: 'edit', value: false })">Cancel</v-btn>
              <v-btn color="success" flat @click.stop="editItem(b$.editData); handleDialog({ name: 'edit', value: false })">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>

        <!-- Delete -->
        <v-dialog v-model="b$.dialogs.delete" max-width="500px">
          <v-card>
            <v-card-title>Delete</v-card-title>
            <v-card-text>
              Are you sure that you want to delete it?
            </v-card-text>
            <v-card-actions>
              <v-btn color="success" flat @click.stop="handleDialog({ name: 'delete', value: false })">Cancel</v-btn>
              <v-btn color="error" flat @click.stop="deleteItem(b$.deleteData); handleDialog({ name: 'delete', value: false })">Confirm</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </aside>
    </v-layout>
  </v-container>
</template>

<script>
// @flow

import { mapGetters, mapActions } from 'vuex';

import { crudOperationsStore } from '../mixins';

import { IBasic } from './types';
import { INITIAL as state } from './constants';
import actions from './actions';
import mutations from './mutations';
import getters from './getters';

export default {
  metaInfo: {
    title: 'CRUD Operations - Basic | Oh My Vue',
    meta: [
      { property: 'og:title', content: 'CRUD Operations - Basic | Oh My Vue' },
    ],
  },
  mixins: [crudOperationsStore],
  computed: {
    b$(): IBasic {
      return this.$store.state.crudOperations.basic;
    },
    ...mapGetters('crudOperations/basic', Object.keys(getters)),
  },
  created() {
    this.$store.registerModule(
      ['crudOperations', 'basic'],
      { namespaced: true, state, actions, mutations, getters },
    );
  },
  methods: {
    ...mapActions('crudOperations/basic', Object.keys(actions)),
  },
};
</script>

<style scoped>
.o-text-field {
  margin: auto 0.33rem;
}

.o-card-title {
  height: 82px;
  transition: all 0.33s;
}
</style>
