<template>
  <div class="page-container">
    <!-- Header -->
    <div class="page-header">
      <div>
        <h1>Schedule Configuration</h1>
        <p class="subtitle">Configure automated URL indexing schedule</p>
      </div>

      <div class="header-actions">
        <button class="btn primary">💾 Save Configuration</button>
      </div>
    </div>

    <!-- Current Site -->
    <div class="card site-card">
      <div class="site-title">Current Site</div>
      <div class="site-value">
        <span class="dot"></span>
        sc-domain:v0-salon-ui-components.vercel.app
      </div>
      <p class="hint">Use the global site selector in the header to change sites</p>
    </div>

    <!-- Main Grid -->
    <div class="grid">
      <!-- Schedule Settings -->
      <div class="card">
        <div class="card-header">
          <span class="icon green">⏰</span>
          <div>
            <h3>Schedule Settings</h3>
            <p>Configure when and how URLs should be processed</p>
          </div>
        </div>

        <!-- Toggle -->
        <div class="toggle-row">
          <label class="toggle">
            <input type="checkbox" v-model="enabled" />
            <span class="slider"></span>
          </label>
          <span>Enable Scheduled Processing</span>
        </div>

        <!-- Form -->
        <div class="form-grid">
          <div class="form-group">
            <label>Frequency</label>
            <select>
              <option>Daily</option>
              <option>Hourly</option>
              <option>Weekly</option>
            </select>
            <small>How often the queue should be processed</small>
          </div>

          <div class="form-group">
            <label>Maximum URLs Per Run</label>
            <input type="number" value="50" />
            <small>Maximum number of URLs per run</small>
          </div>

          <div class="form-group">
            <label>Start Time</label>
            <input type="time" value="08:00" />
            <small>Time of day to start processing</small>
          </div>

          <div class="form-group">
            <label>End Time</label>
            <input type="time" value="20:00" />
            <small>Time of day to stop processing</small>
          </div>

          <div class="form-group full">
            <label>Priority Order</label>
            <input type="text" value="High,Medium,Low" />
            <small>Order in which URL priorities should be processed</small>
          </div>
        </div>
      </div>

      <!-- Right Column -->
      <div class="right-column">
        <!-- Job History -->
        <div class="card">
          <div class="card-header">
            <span class="icon yellow">🕒</span>
            <div>
              <h3>Job History</h3>
              <p>Recent processing jobs</p>
            </div>
          </div>

          <table class="history-table">
            <thead>
              <tr>
                <th>ID</th>
                <th>Start Time</th>
                <th>End Time</th>
                <th>Status</th>
                <th>URLs</th>
                <th>Errors</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td colspan="6" class="empty">No job history available</td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Quick Actions -->
        <div class="card">
          <div class="card-header">
            <span class="icon red">⚡</span>
            <div>
              <h3>Quick Actions</h3>
              <p>Manual processing options</p>
            </div>
          </div>

          <button class="btn primary full">▶ Run Processing Now</button>
          <button class="btn outline full">🗑 Clear Job History</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
const enabled = ref(true)
</script>

<style scoped>
.page-container {
  padding: 28px;
  background: #f9fafb;
}

/* Header */
.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
}

.page-header h1 {
  font-size: 26px;
  font-weight: 700;
}

.subtitle {
  font-size: 14px;
  color: #6b7280;
}

.header-actions {
  display: flex;
  gap: 12px;
}

/* Cards */
.card {
  background: #fff;
  border-radius: 10px;
  border: 1px solid #e5e7eb;
  padding: 20px;
}

/* Site */
.site-card {
  margin-bottom: 24px;
}

.site-title {
  font-size: 13px;
  color: #6b7280;
  margin-bottom: 6px;
}

.site-value {
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 8px;
}

.dot {
  width: 8px;
  height: 8px;
  background: #2563eb;
  border-radius: 50%;
}

/* Grid */
.grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 24px;
}

.right-column {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

/* Card Header */
.card-header {
  display: flex;
  gap: 12px;
  margin-bottom: 16px;
}

.card-header h3 {
  font-size: 16px;
  font-weight: 600;
}

.card-header p {
  font-size: 13px;
  color: #6b7280;
}

.icon {
  width: 32px;
  height: 32px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.icon.green { background: #dcfce7; }
.icon.yellow { background: #fef9c3; }
.icon.red { background: #fee2e2; }

/* Toggle */
.toggle-row {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 20px;
}

.toggle {
  position: relative;
  width: 42px;
  height: 22px;
}

.toggle input {
  display: none;
}

.slider {
  position: absolute;
  inset: 0;
  background: #d1d5db;
  border-radius: 999px;
}

.slider::before {
  content: '';
  position: absolute;
  height: 18px;
  width: 18px;
  left: 2px;
  top: 2px;
  background: #fff;
  border-radius: 50%;
  transition: 0.2s;
}

.toggle input:checked + .slider {
  background: #2563eb;
}

.toggle input:checked + .slider::before {
  transform: translateX(20px);
}

/* Form */
.form-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
}

.form-group {
  display: flex;
  flex-direction: column;
}

.form-group.full {
  grid-column: span 2;
}

label {
  font-size: 13px;
  font-weight: 500;
  margin-bottom: 4px;
}

input,
select {
  padding: 8px;
  border-radius: 6px;
  border: 1px solid #d1d5db;
}

small {
  font-size: 12px;
  color: #6b7280;
}

/* Table */
.history-table {
  width: 100%;
  border-collapse: collapse;
}

.history-table th {
  text-align: left;
  font-size: 12px;
  color: #6b7280;
  padding-bottom: 8px;
}

.history-table td {
  padding: 14px 0;
}

.empty {
  text-align: center;
  color: #9ca3af;
}

/* Buttons */
.btn {
  padding: 8px 14px;
  border-radius: 6px;
  cursor: pointer;
  border: 1px solid transparent;
}

.btn.primary {
  background: #2563eb;
  color: #fff;
}

.btn.outline {
  background: #fff;
  border: 1px solid #d1d5db;
}

.btn.full {
  width: 100%;
  margin-top: 10px;
}

/* Responsive */
@media (max-width: 900px) {
  .grid {
    grid-template-columns: 1fr;
  }
}
</style>
