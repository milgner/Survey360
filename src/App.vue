<template>
  <div id="app">
    <Survey :survey="survey"></Survey>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import * as SurveyVue from "survey-vue";

import "bootstrap/dist/css/bootstrap.css";
(SurveyVue.Survey as any).cssType = "bootstrap";

function genitiveName(params: any[]): string | null {
  const name = params[0];
  if (name === null) {
    return null;
  }
  return name + (name[name.length - 1] == "s" ? "'" : "s");
}
SurveyVue.FunctionFactory.Instance.register("genitiveName", genitiveName);

function questions() {
  return [
    {
      type: "text",
      name: "name",
      isRequired: true,
      valueName: "name",
      title: "Für wen ist dieses Feedback?"
    },
    {
      type: "matrixdynamic",
      minRowCount: 1,
      rowCount: 1,
      valueName: "projects",
      name: "collaboration_projects",
      title: "In welchen Projekten hast Du mit {name} zusammengearbeitet?",
      addRowText: "Weiteres Projekt hinzufügen",
      columns: [
        {
          name: "name",
          isRequired: true,
          title: "Name des Projekts",
          cellType: "text"
        }
      ]
    },
    {
      type: "paneldynamic",
      renderMode: "list",
      allowAddPanel: false,
      allowRemovePanel: false,
      name: "array_project_specific",
      title: "Eure gemeinsamen Projekte",
      valueName: "projects",
      templateTitle: "Projekt {panel.name}",
      templateElements: [
        {
          type: "rating",
          title:
            "Wie bewertest Du {genitiveName} Leistung im Projekt {panel.name}?",
          name: "project_overall",
          valueName: "overall",
          isRequired: true,
          rateValues: [
            {
              value: 1,
              text: "Untere 10%"
            },
            {
              value: 2,
              text: "Untere 30%"
            },
            {
              value: 3,
              text: "Mittlere 30%"
            },
            {
              value: 4,
              text: "Obere 30%"
            },
            {
              value: 5,
              text: "Obere 10%"
            }
          ]
        },
        {
          type: "text",
          title:
            "Was hat {name} im Projekt {panel.name} besonders gut gemacht?",
          name: "project_highlight",
          valueName: "highlight",
          visibleIf: "{panel.overall} > 2",
          requiredIf: "{panel.overall} > 4"
        },
        {
          type: "text",
          title:
            "Was hätte {name} im Projekt {panel.name} besser machen können?",
          name: `project_improvement`,
          valueName: "improvement",
          requiredIf: "{panel.overall} < 4"
        }
      ]
    }
  ];
}
@Component({
  components: {
    Survey: SurveyVue.Survey
  },
  data() {
    //Define Survey JSON
    //Here is the simplest Survey with one text question
    const json = {
      elements: questions(),
      calculatedValues: [
        {
          name: "genitiveName",
          expression: "genitiveName({name})"
        }
      ]
    };

    //Create the model and pass it into VueSJ Survey component
    const model = new SurveyVue.Model(json);
    //You may set model properties
    // model.mode="display"

    return {
      survey: model
    };
  }
})
export default class App extends Vue {}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
