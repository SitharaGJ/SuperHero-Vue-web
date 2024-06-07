<template>
  <v-app>
    <div class="mx-auto mt-5 justify-center align-center">
      <v-row class="justify-center align-center my-4">
        <h2>Super Heroes</h2>
      </v-row>

      <v-dialog max-width="500">
        <template v-slot:activator="{ props: activatorProps }">
          <v-btn v-bind="activatorProps" color="blue" text="Add New Hero+" variant="flat"></v-btn>
        </template>

        <template v-slot:default="{ isActive }">
          <v-card>
            <v-card-text>
              <v-card class="mx-auto" max-width="344" title="Add New Super Hero">
                <v-container>
                  <v-text-field
                    v-model="newHero.Name"
                    color="primary"
                    label="Seper Hero name"
                    variant="underlined"
                  ></v-text-field>

                  <v-text-field
                    v-model="newHero.FirstName"
                    color="primary"
                    label="First name"
                    variant="underlined"
                  ></v-text-field>

                  <v-text-field
                    v-model="newHero.LastName"
                    color="primary"
                    label="Last Name"
                    variant="underlined"
                  ></v-text-field>

                  <v-text-field
                    v-model="newHero.Place"
                    color="primary"
                    label="Place"
                    variant="underlined"
                  ></v-text-field>
                </v-container>

                <v-divider></v-divider>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn small elevation="" color="green" @click="AddNewHero()">Save Hero</v-btn>
                </v-card-actions>
              </v-card>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn text="Close" @click="isActive.value = false"></v-btn>
            </v-card-actions>
          </v-card>
        </template>
      </v-dialog>

      <ul v-if="heroes.length">
        <v-card v-for="hero in heroes" :key="hero.id" class="mt-2 pa-2" style="background-color: darkgrey">
          <div>
            <div>{{ hero.name }} ({{ hero.firstName }} {{ hero.lastName }}) - {{ hero.place }}</div>
          </div>
          <br />

          <v-row cols="2" class="justify-end">
            <v-col cols="auto">
              <v-btn
                density="comfortable"
                color="orange"
                icon="$edit"
                @click="editHero(hero.id), (isActive.value = true)"
              >
              </v-btn>
            </v-col>

            <v-col cols="auto">
              <v-btn density="comfortable" color="red" icon="$delete" @click="deleteHero(hero.id)"></v-btn>
            </v-col>
          </v-row>
        </v-card>
      </ul>
    </div>
  </v-app>
</template>

<script>
export default {
  name: "HomeView",

  data() {
    return {
      heroes: [],
      isActive:{
        value: false
      },
      newHero: {
        name: "",
        firstName: "",
        lastName: "",
        place: "",
      },
    };
  },

  methods: {
    async fetchData() {
      try {
        const response = await fetch("https://localhost:7112/api/SuperHero/getHero");
        const data = await response.json();
        this.heroes = data;
      } catch (error) {
        console.error("Error fetching data:", error);
      }
    },

    async AddNewHero() {
      try {
        const response = await fetch("https://localhost:7112/api/SuperHero/createHero/", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify(this.newHero),
        });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        // Clear the form after successful submission
        this.newHero = {
          name: "",
          firstName: "",
          lastName: "",
          place: "",
        };
        // Fetch the updated list of heroes
        await this.fetchData();
      } catch (error) {
        console.error("Error sending data:", error);
      }
    },

    async deleteHero(id) {
      try {
        const response = await fetch(`https://localhost:7112/api/SuperHero/deleteHero/${id}`, {
          method: "DELETE",
        });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        // Fetch the updated list of heroes after successful deletion
        await this.fetchData();
      } catch (error) {
        console.error("Error deleting hero:", error);
      }
    },
    async editHero(id) {
      try {
        const response = await fetch(`https://localhost:7112/api/SuperHero/getHero/${id}`, {
          method: "GET",
        });
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        this.newHero = data;
      } catch (error) {
        console.error("Error fetching data:", error);
      }
    },
  },

  mounted() {
    this.fetchData();
  },
};
</script>
