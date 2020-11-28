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

const COMPARABLE_RATING = [
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
];

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
          rateValues: COMPARABLE_RATING
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
    },
    {
      type: "text",
      name: "success_story",
      title:
        "Welches Problem habt ihr gemeinsam erfolgreich gemeistert und was hat diesen Erfolg möglich gemacht?"
    },
    {
      type: "matrix",
      name: "general_skills",
      title:
        "Wie gut schlägt sich {name} in Bezug auf die folgenden Eigenschaften?",
      columns: COMPARABLE_RATING,
      rows: [
        { value: "ethics", text: "Hält ethische Standards hoch" },
        { value: "learning_from_mistakes", text: "Lernt aus Fehlern" },
        { value: "customer_needs", text: "Auf Kunden-Bedürfnisse fokussiert" },
        { value: "problem_solving", text: "Lösungs-orientiert" }
      ]
    },
    {
      type: "matrix",
      name: "collaboration",
      title: "Noch ein paar Punkte zur Zusammenarbeit",
      columns: COMPARABLE_RATING,
      rows: [
        {
          value: "inspires_learning",
          text:
            "Inspiriert andere, zu lernen, besser zu werden und ihre Ziele zu erreichen"
        },
        {
          value: "conflict_solution",
          text: "Löst Konflikte auf angemessene Art, auch aus Eigen-Initiative"
        },
        {
          value: "effective_communication",
          text: "Kommuniziert offen & effektiv"
        },
        {
          value: "giving_feedback",
          text: "Gibt konstruktives und hilfreiches Feedback"
        },
        {
          value: "open_to_innovation",
          text: "Ist offen für Innovation & Veränderung"
        }
      ]
    },
    {
      type: "rating",
      name: "contribution_to_success",
      title: "{name} hilft mir, meinen Job gut zu machen",
      rateValues: [
        { value: 0, text: "Überhaupt nicht" },
        { value: 1, text: "Ein wenig" },
        { value: 2, text: "Okay-ish" },
        { value: 3, text: "Gut" },
        { value: 4, text: "Sehr gut" }
      ]
    },
    {
      type: "rating",
      name: "representativeness",
      title:
        "Wie viele Leute vom Rest des Teams, denkst Du, teilen Deine Einschätzung von {name}?",
      rateValues: [
        {
          value: 1,
          text: "Kaum einer"
        },
        {
          value: 2,
          text: "Einige"
        },
        {
          value: 3,
          text: "Die meisten"
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
