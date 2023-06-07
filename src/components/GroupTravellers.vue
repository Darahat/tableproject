<template>
  <v-expansion-panels>
    <v-expansion-panel>
      <!-- <v-expansion-panel-header>Travellers</v-expansion-panel-header> -->
      <v-expansion-panel-header>
        <v-row no-gutters>
          <v-col> Travellers </v-col>
          <v-col cols="1" class="ml-auto">
            <v-fade-transition leave-absolute>
              <v-chip label>10</v-chip>
            </v-fade-transition>
          </v-col>
        </v-row>
      </v-expansion-panel-header>
      <v-expansion-panel-content>
        <v-form ref="travellerForm" v-model="travellerFormValid">
          <v-data-table v-model="selectedTravellers" :headers="travellerHeaders" :title="titles" :items="travellers"
            :search="travellerSearch" show-select disable-sort item-key="id">
            <template v-slot:top>
              <v-toolbar flat>
                <v-text-field v-model="travellerSearch" prepend-inner-icon="mdi-magnify" label="Search" single-line
                  hide-details clearable></v-text-field>

                <v-spacer></v-spacer>

                <v-menu :close-on-content-click="false" @input="travellerAssignMenuInput" offset-y left>
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn small v-bind="attrs" v-on="on" :disabled="!selectedTravellers.length" class="mr-2">
                      Assign
                    </v-btn>
                  </template>
                  <v-list flat>
                    <v-list-item-group v-model="selectedItineraryAssign" multiple>
                      <v-list-item v-for="(option, oIndex) in itineraryAssignOptions" :key="oIndex">
                        <template v-slot:default="{ active }">
                          <v-list-item-content>
                            <v-list-item-title>{{ option }}</v-list-item-title>
                            <v-list-item-subtitle>More information</v-list-item-subtitle>
                          </v-list-item-content>
                          <v-list-item-action>
                            <v-checkbox :input-value="active"></v-checkbox>
                          </v-list-item-action>
                        </template>
                      </v-list-item>
                    </v-list-item-group>
                  </v-list>
                </v-menu>

                <v-menu :close-on-content-click="false" offset-y left>
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn small v-bind="attrs" v-on="on" class="mr-2" disabled>
                      Columns
                    </v-btn>
                  </template>
                  <v-list flat>
                    <v-list-item-group v-model="selectedTravllerColumns" multiple>
                      <v-list-item v-for="(column, cIndex) in travellerColumnOptions" :key="cIndex">
                        <template v-slot:default="{ active }">
                          <v-list-item-content>
                            <v-list-item-title>{{ column }}</v-list-item-title>
                          </v-list-item-content>
                          <v-list-item-action>
                            <v-checkbox :input-value="active"></v-checkbox>
                          </v-list-item-action>
                        </template>
                      </v-list-item>
                    </v-list-item-group>
                  </v-list>
                </v-menu>

                <v-btn color="primary" small @click="saveTravellers">
                  Save
                </v-btn>
              </v-toolbar></template>
            <template v-slot:[`title.title`]="{ index }">
              <v-autocomplete v-model="title[index].title" :items="titleOptions" hide-details="auto" outlined dense
                required :rules="travellerRules.name"></v-autocomplete>
            </template>
            <template v-slot:[`item.firstName`]="{ index }">
              <v-text-field v-model="travellers[index].firstName" hide-details="auto" outlined dense required
                :rules="travellerRules.name"></v-text-field>
            </template>
            <template v-slot:[`item.lastName`]="{ index }">
              <v-text-field v-model="travellers[index].lastName" hide-details="auto" outlined dense required
                :rules="travellerRules.name"></v-text-field>
            </template>
            <template v-slot:[`item.email`]="{ index }">
              <v-text-field v-model="travellers[index].email" @focus="addTravellerRow(index)" hide-details="auto" outlined
                dense></v-text-field>
            </template>
            <template v-slot:[`item.actions`]="{ index }">
              <v-btn v-if="travellers.length > 1" icon small title="Delete" @click="removeTravellerRow(index)">
                <v-icon>mdi-close</v-icon>
              </v-btn>
            </template>

            <!-- <template v-slot:[`footer.prepend`]="">
                    <v-autocomplete
                      label="Select travellers"
                      :items="['Peter', 'Michael']"
                      hide-details="auto"
                      outlined
                      dense
                      hide-selected
                      required
                      multiple
                      class="mr-6"
                    ></v-autocomplete>
                  </template> -->
          </v-data-table>
        </v-form>
      </v-expansion-panel-content>
    </v-expansion-panel>
  </v-expansion-panels>
</template>

<script>
export default {
  name: "GroupTravellers",
  data() {
    return {
      travellerFormValid: false,
      selectedTravellers: [],
      travellerHeaders: [
        { text: "Title", value: "title", width: 150 },
        { text: "First name", value: "firstName" },
        { text: "Last name", value: "lastName" },
        { text: "Email", value: "email" },
        { text: "", value: "actions" },
      ],
      titles: [{
        title: "Mr",
      },
      {
        title: "Mrs",
      },
      {
        title: "Ms",
      }],
      travellers: [

        {
          id: 0,
          title: "Mr",
          firstName: "Peter",
          lastName: "Pan",
          email: "test@email.com",
        },
        {
          id: 1,
          title: "Mr",
          firstName: "Brad",
          lastName: "Pitt",
        },
      ],
      travellerSearch: "",
      travellerRules: {
        name: [(val) => (val || "").length > 0 || "This field is required"],
      },
      titleOptions: '',

      // titleOptions: this.$CONSTANTS.titles,
      itineraryAssignOptions: ["Flight 1", "Hotel 2"],
      selectedItineraryAssign: [],
      travellerColumnOptions: ["Email", "Passport", "Phone"],
      selectedTravllerColumns: [],
    };
  },
  // watch: {
  //   'travellers[travellers.length - 1].firstName': {
  //     handler(value) {
  //       const lastIndex = this.travellers.length - 1;
  //       const lastTraveller = this.travellers[lastIndex];
  //       if (value && lastTraveller.lastName) {
  //         this.travellers.push({ id: lastIndex + 1 });
  //       }
  //     },
  //     deep: true,
  //   },
  //   'travellers[travellers.length - 1].lastName': {
  //     handler(value) {
  //       const lastIndex = this.travellers.length - 1;
  //       const lastTraveller = this.travellers[lastIndex];
  //       if (value && lastTraveller.firstName) {
  //         this.travellers.push({ id: lastIndex + 1 });
  //       }
  //     },
  //     deep: true,
  //   },
  // },

  methods: {
    addTravellerRow(index) {
      if (!this.$refs.travellerForm.validate()) {
        return;
      }
      // only push if focus on last row
      if (index === this.travellers.length - 1) {
        this.travellers.push({ id: index + 1 });
      }
    },
    saveTravellers() {
      if (!this.$refs.travellerForm.validate()) {
        return;
      }
      if (
        this.selectedTravellers.length &&
        !this.selectedItineraryAssign.length
      ) {
        this.$root.vtoast.show({
          message: "No itinerary options assigned to selected travellers",
          color: "error",
        });
        return;
      }

      // link selectedItineraryAssign with selectedTravellers
      // save to DB

      this.selectedTravellers = [];

      console.log("HEY");
      this.$root.vtoast.show({ message: "Saved" });
    },
    travellerAssignMenuInput(open) {
      if (open) {
        this.selectedItineraryAssign = [];
      }
    },
    removeTravellerRow(index) {
      this.travellers.splice(index, 1);
    },
    selectTravellerFromLookup(traveller) {
      this.travellers.splice(-1, 1, traveller.lookup);
    },
  },
};
</script>

<style></style>
