<template>
  <div>
    <div class="feedback-button-container">
      <button 
        class="feedback-button"
        @click="showFeedbackForm = !showFeedbackForm"
      >
        <span>提供反馈</span>
      </button>
    </div>
    <div v-if="showFeedbackForm">
      <div class="feedback-mask" @click="closeFeedbackForm"></div>
      <div class="feedback-form" @click.stop>
        <div class="feedback-form-header">
          <h3>问题反馈</h3>
          <button class="feedback-close" @click="closeFeedbackForm" aria-label="关闭">×</button>
        </div>
        <div class="feedback-form-body">
          <div class="form-row">
            <label class="form-label">反馈类型</label>
            <div class="form-options">
              <label class="option-item" v-for="type in feedbackTypes" :key="type.value">
                <input type="radio" v-model="feedbackType" :value="type.value" />
                <span>{{ type.label }}</span>
              </label>
            </div>
          </div>
          <div class="form-row">
            <label class="form-label" for="feedback-content">反馈内容</label>
            <textarea 
              id="feedback-content" 
              v-model="feedbackContent"
              class="form-textarea"
              placeholder="请详细描述您的问题或建议..."
              rows="4"
            ></textarea>
          </div>
          <div class="form-row">
            <label class="form-label" for="feedback-email">您的邮箱（可选）</label>
            <input 
              type="email" 
              id="feedback-email" 
              v-model="feedbackEmail"
              class="form-input"
              placeholder="example@example.com"
            />
          </div>
        </div>
        <div class="feedback-form-footer">
          <button class="submit-button" @click="submitFeedback">提交反馈</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'FeedbackButton',
  data() {
    return {
      showFeedbackForm: false,
      feedbackType: 'bug',
      feedbackTypes: [
        { value: 'bug', label: 'Bug 报告' },
        { value: 'feature', label: '功能建议' },
        { value: 'content', label: '内容问题' },
        { value: 'other', label: '其他' }
      ],
      feedbackContent: '',
      feedbackEmail: '',
      currentPage: '',
      selectedText: ''
    }
  },
  mounted() {
    console.log('FeedbackButton mounted, setting up event listener');
    // 获取当前页面路径
    if (this.$route && this.$route.path) {
      this.currentPage = this.$route.path;
    } else {
      this.currentPage = '';
    }
    // 使用事件总线监听事件
    if (this.$eventBus) {
      this.$eventBus.$on('show-feedback', this.handleFeedbackEvent);
    }
    // 同时保留原来的监听方式作为备份
    this.$root.$on('show-feedback', this.handleFeedbackEvent);
  },
  beforeDestroy() {
    // 清理事件监听
    if (this.$eventBus) {
      this.$eventBus.$off('show-feedback', this.handleFeedbackEvent);
    }
    this.$root.$off('show-feedback', this.handleFeedbackEvent);
  },
  methods: {
    handleFeedbackEvent(data) {
      console.log('Received feedback event with data:', data);
      this.showFeedbackForm = true;
      if (data && data.selectedText) {
        this.selectedText = data.selectedText;
        this.feedbackContent = `选中的文本: "${this.selectedText}"
\n我的反馈: `;
      }
    },
    submitFeedback() {
      // 在实际应用中，你会将这些数据发送到后端
      console.log('Feedback submitted:', {
        type: this.feedbackType,
        content: this.feedbackContent,
        email: this.feedbackEmail,
        page: this.currentPage,
        selectedText: this.selectedText
      });
      // 显示成功消息
      alert('感谢您的反馈！');
      // 重置表单
      this.feedbackType = 'bug';
      this.feedbackContent = '';
      this.feedbackEmail = '';
      this.selectedText = '';
      this.showFeedbackForm = false;
    },
    closeFeedbackForm() {
      this.showFeedbackForm = false;
    }
  }
}
</script>

<style scoped>
.feedback-button-container {
  position: fixed;
  right: 32px;
  top: 50%;
  transform: translateY(-50%);
  z-index: 1000;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.feedback-button {
  background: #fff;
  color: #1677ff;
  border: 1.5px solid #1677ff;
  border-radius: 24px;
  padding: 10px 28px;
  font-size: 16px;
  font-weight: 600;
  box-shadow: 0 2px 8px rgba(22,119,255,0.08);
  cursor: pointer;
  transition: background 0.2s, color 0.2s, border-color 0.2s;
}
.feedback-button:hover {
  background: #1677ff;
  color: #fff;
  border-color: #0958d9;
}

.feedback-mask {
  position: fixed;
  left: 0;
  top: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0,0,0,0.18);
  z-index: 1999;
}

.feedback-form {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background: #fff;
  border-radius: 18px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.18);
  width: 420px;
  min-width: 320px;
  padding: 0;
  z-index: 2000;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}
.feedback-form-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 24px 32px 0 32px;
}
.feedback-form-header h3 {
  margin: 0;
  color: #222;
  font-size: 20px;
  font-weight: 700;
  letter-spacing: 0.5px;
}
.feedback-close {
  background: none;
  border: none;
  font-size: 24px;
  color: #bbb;
  cursor: pointer;
  transition: color 0.2s;
  padding: 0 0 0 12px;
}
.feedback-close:hover {
  color: #1677ff;
}
.feedback-form-body {
  padding: 16px 32px 0 32px;
  display: flex;
  flex-direction: column;
  gap: 0;
}
.form-row {
  margin-bottom: 18px;
  display: flex;
  flex-direction: column;
}
.form-label {
  font-size: 15px;
  font-weight: 500;
  color: #222;
  margin-bottom: 8px;
}
.form-options {
  display: flex;
  gap: 18px;
}
.option-item {
  display: flex;
  align-items: center;
  font-size: 15px;
  color: #444;
  cursor: pointer;
}
.option-item input[type="radio"] {
  margin-right: 6px;
  accent-color: #1677ff;
}
.form-input, .form-textarea {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #e5e6eb;
  border-radius: 8px;
  font-size: 15px;
  background: #fafbfc;
  transition: border-color 0.2s;
  box-sizing: border-box;
}
.form-input:focus, .form-textarea:focus {
  border-color: #1677ff;
  outline: none;
}
.form-textarea {
  resize: vertical;
  min-height: 60px;
}
.feedback-form-footer {
  display: flex;
  justify-content: flex-end;
  padding: 0 32px 24px 32px;
  background: none;
}
.submit-button {
  background: #1677ff;
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 10px 32px;
  cursor: pointer;
  font-weight: 600;
  font-size: 15px;
  transition: background 0.3s;
  box-shadow: 0 2px 8px rgba(22,119,255,0.08);
}
.submit-button:hover {
  background: #0958d9;
}
@media (max-width: 768px) {
  .feedback-button-container {
    right: 10px;
  }
  .feedback-form {
    width: 96vw;
    min-width: 0;
  }
  .feedback-form-header, .feedback-form-body, .feedback-form-footer {
    padding-left: 8px;
    padding-right: 8px;
  }
}
</style>
