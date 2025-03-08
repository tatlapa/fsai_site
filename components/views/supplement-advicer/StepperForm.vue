<script setup lang="ts">
import { Button } from "@/components/ui/button";
import {
  Form,
  FormControl,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
  FormDescription,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import {
  Select,
  SelectContent,
  SelectGroup,
  SelectItem,
  SelectTrigger,
  SelectValue,
} from "@/components/ui/select";
import {
  Stepper,
  StepperDescription,
  StepperItem,
  StepperSeparator,
  StepperTitle,
  StepperTrigger,
} from "@/components/ui/stepper";
import { toast } from "@/components/ui/toast";
import { toTypedSchema } from "@vee-validate/zod";
import { Check, Circle, Dot } from "lucide-vue-next";
import { h, ref } from "vue";
import * as z from "zod";

const goals = [
  { id: "energy_focus", label: "Energy & Focus" },
  { id: "sleep_improvement", label: "Sleep Improvement" },
  { id: "stress_management", label: "Stress Management" },
  { id: "physical_performance", label: "Physical Performance" },
  { id: "mental_clarity", label: "Mental Clarity" },
  { id: "immune_support", label: "Immune Support" },
  { id: "anti_aging", label: "Anti-aging" },
  { id: "weight_management", label: "Weight Management" },
];

const issues = [
  { id: "anxiety", label: "Anxiety" },
  { id: "depression", label: "Depression" },
  { id: "insomnia", label: "Insomnia" },
  { id: "joint_pain", label: "Joint Pain" },
  { id: "digestive_issues", label: "Digestive Issues" },
  { id: "high_blood_pressure", label: "High Blood Pressure" },
  { id: "diabetes", label: "Diabetes" },
  { id: "none", label: "None" },
];

const formSchema = [
  z.object({
    age: z.number().min(1).max(120),
    gender: z.string(),
  }),
  z.object({
    goals: z.array(z.string()).min(1, "Select at least one goal"),
    // issues: z.array(z.string()).min(0),
  }),
  z.object({
    sleepQuality: z.string(),
    stressLevel: z.string(),
  }),
];

const stepIndex = ref(1);

const steps = [
  {
    step: 1,
    title: "Basic Info",
  },
  {
    step: 2,
    title: "Health Goals",
  },
  {
    step: 3,
    title: "Lifestyle",
  },
  // {
  //   step: 4,
  //   title: "Additional Info",
  // },
];

function onSubmit(values: any) {
  toast({
    title: "You submitted the following values:",
    description: h(
      "pre",
      { class: "mt-2 w-[340px] rounded-md bg-slate-950 p-4" },
      h("code", { class: "text-white" }, JSON.stringify(values, null, 2))
    ),
  });
}
</script>

<template>
  <div class="w-1/2">
    <Form
      v-slot="{ meta, values, validate }"
      as=""
      keep-values
      :validation-schema="toTypedSchema(formSchema[stepIndex - 1])"
    >
      <Stepper
        v-slot="{ isNextDisabled, isPrevDisabled, nextStep, prevStep }"
        v-model="stepIndex"
        class="block w-full"
      >
        <form
          @submit="
            (e) => {
              e.preventDefault();
              validate();

              if (stepIndex === steps.length && meta.valid) {
                onSubmit(values);
              }
            }
          "
          class="flex flex-col gap-12 mb-12"
        >
          <div class="flex w-full flex-start gap-2">
            <StepperItem
              v-for="step in steps"
              :key="step.step"
              v-slot="{ state }"
              class="relative flex w-full flex-col items-center justify-center"
              :step="step.step"
            >
              <StepperSeparator
                v-if="step.step !== steps[steps.length - 1].step"
                class="absolute left-[calc(50%+20px)] right-[calc(-50%+10px)] top-5 block h-0.5 shrink-0 rounded-full bg-muted group-data-[state=completed]:bg-primary"
              />

              <StepperTrigger as-child>
                <Button
                  :variant="
                    state === 'completed' || state === 'active'
                      ? 'default'
                      : 'outline'
                  "
                  size="icon"
                  class="z-10 rounded-full shrink-0"
                  :class="[
                    state === 'active' &&
                      'ring-2 ring-ring ring-offset-2 ring-offset-background',
                  ]"
                  :disabled="state !== 'completed' && !meta.valid"
                >
                  <Check v-if="state === 'completed'" class="size-5" />
                  <Circle v-if="state === 'active'" />
                  <Dot v-if="state === 'inactive'" />
                </Button>
              </StepperTrigger>

              <div class="mt-5 flex flex-col items-center text-center">
                <StepperTitle
                  :class="[state === 'active' && 'text-primary']"
                  class="text-sm font-semibold transition lg:text-base"
                >
                  {{ step.title }}
                </StepperTitle>
              </div>
            </StepperItem>
          </div>

          <div class="rounded-lg p-4 shadow-lg">
            <div class="flex flex-col gap-4 mt-4">
              <template v-if="stepIndex === 1">
                <FormField v-slot="{ componentField }" name="age">
                  <FormItem>
                    <FormLabel>Age</FormLabel>
                    <FormControl>
                      <Select type="number" v-bind="componentField">
                        <SelectTrigger>
                          <SelectValue placeholder="Select an age" />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectGroup>
                            <SelectLabel>Age</SelectLabel>
                            <SelectItem
                              v-for="age in 120"
                              :key="age"
                              :value="age"
                            >
                              {{ age }}
                            </SelectItem>
                          </SelectGroup>
                        </SelectContent>
                      </Select>
                    </FormControl>
                    <FormMessage />
                  </FormItem>
                </FormField>

                <FormField v-slot="{ componentField }" name="gender">
                  <FormItem>
                    <FormLabel>Gender</FormLabel>
                    <FormControl>
                      <Select type="text" v-bind="componentField">
                        <SelectTrigger>
                          <SelectValue placeholder="Select a gender" />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectGroup>
                            <SelectLabel>Gender</SelectLabel>
                            <SelectItem value="male"> Male </SelectItem>
                            <SelectItem value="female"> Female </SelectItem>
                            <SelectItem value="other"> Other </SelectItem>
                          </SelectGroup>
                        </SelectContent>
                      </Select>
                    </FormControl>
                    <FormMessage />
                  </FormItem>
                </FormField>
              </template>

              <template v-if="stepIndex === 2">
                <FormField v-slot="{ value = [], handleChange }" name="goals">
                  <FormItem>
                    <FormLabel>Health Goals</FormLabel>
                    <FormDescription>Select at least one goal</FormDescription>
                    <FormControl>
                      <div class="grid grid-cols-3 gap-1 items-center">
                        <div
                          v-for="goal in goals"
                          :key="goal.id"
                          class="flex items-center gap-2"
                        >
                          <Checkbox
                            :model-value="value.includes(goal.id)"
                            @update:model-value="
                              (checked) => {
                                const updatedGoals = checked
                                  ? [...value, goal.id]
                                  : value.filter((g) => g !== goal.id);

                                handleChange(updatedGoals); // 🔥 Met à jour Vee-Validate
                              }
                            "
                          />
                          <label>{{ goal.label }}</label>
                        </div>
                      </div>
                    </FormControl>
                    <FormMessage />
                  </FormItem>
                </FormField>

                <FormField v-slot="{ value = [], handleChange }" name="issues">
                  <FormItem>
                    <FormLabel>Health Issues</FormLabel>
                    <FormDescription>Select at least one issue</FormDescription>
                    <FormControl>
                      <div class="grid grid-cols-3 gap-1 items-center">
                        <div
                          v-for="issue in issues"
                          :key="issue.id"
                          class="flex items-center gap-2"
                        >
                          <Checkbox
                            :model-value="value.includes(issue.id)"
                            @update:model-value="
                              (checked) => {
                                const updatedIssues = checked
                                  ? [...value, issue.id]
                                  : value.filter((i) => i !== issue.id);

                                handleChange(updatedIssues); // 🔥 Met à jour Vee-Validate
                              }
                            "
                          />
                          <label>{{ issue.label }}</label>
                        </div>
                      </div>
                    </FormControl>
                    <FormMessage />
                  </FormItem>
                </FormField>
              </template>

              <template v-if="stepIndex === 3">
                <FormField v-slot="{ componentField }" name="sleepQuality">
                  <FormItem>
                    <FormLabel>Sleep Quality </FormLabel>
                    <Select v-bind="componentField">
                      <FormControl>
                        <SelectTrigger>
                          <SelectValue placeholder="Select sleep quality" />
                        </SelectTrigger>
                      </FormControl>
                      <SelectContent>
                        <SelectGroup>
                          <SelectLabel>Sleep Quality </SelectLabel>
                          <SelectItem value="excellent"> Excellent </SelectItem>
                          <SelectItem value="good"> Good </SelectItem>
                          <SelectItem value="fair"> Fair </SelectItem>
                          <SelectItem value="poor"> Poor </SelectItem>
                        </SelectGroup>
                      </SelectContent>
                    </Select>
                    <FormMessage />
                  </FormItem>
                </FormField>

                <FormField v-slot="{ componentField }" name="stressLevel">
                  <FormItem>
                    <FormLabel>Stress Level </FormLabel>
                    <FormControl>
                      <Select type="text" v-bind="componentField">
                        <SelectTrigger>
                          <SelectValue placeholder="Select a stress level" />
                        </SelectTrigger>
                        <SelectContent>
                          <SelectGroup>
                            <SelectLabel>Stress Level </SelectLabel>
                            <SelectItem value="low"> Low </SelectItem>
                            <SelectItem value="moderate"> Moderate </SelectItem>
                            <SelectItem value="high"> High </SelectItem>
                            <SelectItem value="severe"> Severe </SelectItem>
                          </SelectGroup>
                        </SelectContent>
                      </Select>
                    </FormControl>
                    <FormMessage />
                  </FormItem>
                </FormField>
              </template>
            </div>

            <div class="flex items-center justify-between mt-4">
              <Button
                :disabled="isPrevDisabled"
                variant="outline"
                size="sm"
                @click="prevStep()"
              >
                Back
              </Button>
              <div class="flex items-center gap-3">
                <Button
                  v-if="stepIndex !== 3"
                  :type="meta.valid ? 'button' : 'submit'"
                  :disabled="isNextDisabled"
                  size="sm"
                  @click="meta.valid && nextStep()"
                >
                  Next
                </Button>
                <Button v-if="stepIndex === 3" size="sm" type="submit">
                  Submit
                </Button>
              </div>
            </div>
          </div>
        </form>
      </Stepper>
    </Form>
  </div>
</template>
