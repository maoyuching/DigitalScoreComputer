<template>
  <div id="app">
    <div class="floating-score">
      当前得分: {{ score }}
    </div>
	
    <div class="questionnaire">

      <form @submit.prevent="submitForm">
        <!-- 填空题 -->
        <div v-for="(question, index) in questions" :key="index" class="question">
          <label>{{ question.text }}</label>
          <input v-if="question.type === 'text'" type="text" v-model="answers[index]" />
		  
          <!-- 单选题 -->
          <div v-else-if="question.type === 'radio'">
            <label v-for="option in question.options" :key="option.id">
              <input type="radio" :value="option.value" :name="'question_' + index" v-model="answers[index]" />
              {{ option.text }} ({{ option.score }})
            </label>
          </div>
		  
          <!-- 多选题 -->
          <div v-else-if="question.type === 'checkbox'">
            <label v-for="option in question.options" :key="option.id">
              <input type="checkbox" :value="option.value" :name="'question_' + index" v-model="answers[index]">
              {{ option.text }} ({{ option.score }})
            </label>
          </div>
        </div>
        <button type="submit">提交问卷</button>
      </form>
    </div>
  </div>
</template>


<script>
import { ref, computed } from 'vue';

export default {
  name: 'App',
  setup() {
    // 定义问卷题目和选项
    const questions = ref([
      {
        text: '你最喜欢的颜色是什么？',
        type: 'text',
      },
      {
        text: '你最喜欢的编程语言是什么？',
        options: [
          { text: 'JavaScript', value: 'js', score: 5 },
          { text: 'Python', value: 'py', score: 3 },
          { text: 'Java', value: 'java', score: 2 },
        ],
        type: 'radio',
      },
      {
        text: '你经常使用的前端框架有哪些？',
        options: [
          { text: 'Vue', value: 'vue', score: 5 },
          { text: 'React', value: 'react', score: 3 },
          { text: 'Angular', value: 'angular', score: 2 },
        ],
        type: 'checkbox',
      },
    ]);

    // 用户的答案
    const answers = ref(questions.value.map(() => ''));

    // 计算得分
    const score = computed(() => {
      let totalScore = 0;
      questions.value.forEach((question, index) => {
        if (question.type === 'text') {
          // 假设填空题每填写一个得1分
          totalScore += answers.value[index].length > 0 ? 1 : 0;
        } else if (question.type === 'radio' || question.type === 'checkbox') {
          const selectedOption = question.options.find(option => option.value === answers.value[index]);
          if (selectedOption) {
            totalScore += selectedOption.score;
          }
        }
      });
      return totalScore;
    });

    // 提交问卷
    const submitForm = () => {
      alert('问卷提交成功，得分：' + score.value);
    };

    return {
      questions,
      answers,
      score,
      submitForm,
    };
  },
};
</script>

<style>
	.floating-score {
	  position: fixed;
	  top: 50px;
	  right: 10px;
	  background: #f9f9f9;
	  padding: 10px;
	  border: 1px solid #ccc;
	  border-radius: 5px;
	}
	
	.questionnaire {
	  padding: 20px;
	  margin-top: 50px; /* 留出空间给浮动得分 */
	}
	
	.question {
	  margin-bottom: 20px;
	}
	
	label {
	  margin-right: 10px;
	}
	
	input[type="text"],
	input[type="radio"],
	input[type="checkbox"] {
	  margin-right: 5px;
	}
	
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
