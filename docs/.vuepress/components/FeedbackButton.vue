<template>
  <div class="feedback-button-container">
    <button 
      class="feedback-button"
      @click="showFeedbackForm = !showFeedbackForm"
    >
      <span v-if="!showFeedbackForm">提供反馈</span>
      <span v-else>关闭</span>
    </button>
    
    <div class="feedback-form" v-if="showFeedbackForm">
      <h3>问题反馈</h3>
      <div class="form-group">
        <label for="feedback-type">反馈类型</label>
        <select id="feedback-type" v-model="feedbackType">
          <option value="bug">Bug 报告</option>
          <option value="feature">功能建议</option>
          <option value="content">内容问题</option>
          <option value="other">其他</option>
        </select>
      </div>
      
      <div class="form-group">
        <label for="feedback-content">反馈内容</label>
        <textarea 
          id="feedback-content" 
          v-model="feedbackContent"
          placeholder="请详细描述您的问题或建议..."
          rows="4"
        ></textarea>
      </div>
      
      <div class="form-group">
        <label for="feedback-email">您的邮箱 (可选)</label>
        <input 
          type="email" 
          id="feedback-email" 
          v-model="feedbackEmail"
          placeholder="example@example.com"
        />
      </div>
      
      <div class="form-actions">
        <button class="submit-button" @click="submitFeedback">提交反馈</button>
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
      feedbackContent: '',
      feedbackEmail: '',
      currentPage: '',
      selectedText: ''
    }
  },
  mounted() {
    console.log('FeedbackButton mounted, setting up event listener');
    // 获取当前页面路径
    this.currentPage = this.$route.path;
    
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
        this.feedbackContent = `选中的文本: "${this.selectedText}"\n\n我的反馈: `;
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
    }
  }
}
</script>

<style scoped>
.feedback-button-container {
  position: fixed;
  right: 20px; /* 按钮贴在右侧，距离边缘20px */
  top: 50%;
  transform: translateY(-50%); /* 保持垂直居中 */
  z-index: 1000;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.feedback-button {
  background-color: #3eaf7c;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 10px 15px;
  cursor: pointer;
  font-weight: bold;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  transition: background-color 0.3s;
}

.feedback-button:hover {
  background-color: #2c8f63;
}

.feedback-form {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background-color: white;
  border-radius: 4px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
  width: 300px;
  border: 1px solid #eaecef;
  z-index: 2000;
}

.feedback-form h3 {
  margin-top: 0;
  margin-bottom: 15px;
  color: #3eaf7c;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  font-size: 0.9em;
  color: #2c3e50;
}

.form-group input,
.form-group select,
.form-group textarea {
  width: 100%;
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 0.9em;
}

.form-group textarea {
  resize: vertical;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
}

.submit-button {
  background-color: #3eaf7c;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 15px;
  cursor: pointer;
  font-weight: bold;
  transition: background-color 0.3s;
}

.submit-button:hover {
  background-color: #2c8f63;
}

@media (max-width: 768px) {
  .feedback-button-container {
    right: 10px;
  }
  
  .feedback-form {
    width: 250px;
  }
}
</style>
