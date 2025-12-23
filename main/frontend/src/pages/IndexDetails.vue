<template>
  <div class="page-container">
    <div class="page-header">
      <router-link to="/index" class="back-link">← Back to Google Indexing</router-link>
      <h1>{{ siteName }} - {{ siteUrl }}</h1>
      <p class="subtitle">URLs from the last crawl.</p>
    </div>

    <!-- Summary Card -->
    <div class="summary-card">
      <div class="summary-item">
        <span class="label">Total URLs</span>
        <span class="value">{{ totalUrlCount }}</span>
      </div>
      <div class="summary-item">
        <span class="label">Success</span>
        <span class="value success">{{ successCount }}</span>
      </div>
      <div class="summary-item">
        <span class="label">Failed</span>
        <span class="value failed">{{ failedCount }}</span>
      </div>
    </div>

    <!-- URLs Table with Pagination -->
    <div class="table-card">
      <table class="urls-table">
        <thead>
          <tr>
            <th>URL</th>
            <th>Status</th>
            <th>Status Code</th>
            <th>Indexed At</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="url in paginatedUrls" :key="url.id" :class="getUrlRowClass(url.status)">
            <td class="url-cell">
              <a :href="url.url" target="_blank" rel="noopener noreferrer">{{ url.url }}</a>
            </td>
            <td>
              <span class="status-badge" :class="getUrlStatusClass(url.status)">
                {{ url.status }}
              </span>
            </td>
            <td class="status-code">{{ url.statusCode }}</td>
            <td>{{ formatDate(url.crawledAt) }}</td>
            <td class="action-cell">
              <button class="reindex-btn" @click="reIndex(url.id)">Re-Index</button>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Pagination -->
      <div class="pagination">
        <button 
          @click="previousPage" 
          :disabled="currentPage === 1"
          class="pagination-btn"
        >
          Previous
        </button>
        <div class="pagination-info">
          Page {{ currentPage }} of {{ totalPages }} ({{ totalUrlCount }} total URLs)
        </div>
        <button 
          @click="nextPage" 
          :disabled="currentPage === totalPages"
          class="pagination-btn"
        >
          Next
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { useRoute } from 'vue-router'

// ✅ Updated to include "Queue"
type StatusType = 'Success' | 'Failed' | 'Queue'

interface CrawledUrl {
  id: number
  url: string
  status: StatusType
  statusCode: number
  crawledAt: Date
}

interface SiteData {
  id: number
  name: string
  url: string
  type: string
  urls: CrawledUrl[]
}

const route = useRoute()
const currentPage = ref(1)
const itemsPerPage = 10

// Sample data
const sitesData: SiteData[] = [
  {
    id: 1,
    name: 'Main Website',
    url: 'https://example.com',
    type: 'Corporate',
    urls: [
      { id: 1, url: 'https://example.com/', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:30') },
      { id: 2, url: 'https://example.com/about', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:31') },
      { id: 3, url: 'https://example.com/services', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:32') },
      { id: 4, url: 'https://example.com/contact', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:33') },
      { id: 5, url: 'https://example.com/blog', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:34') },
      { id: 6, url: 'https://example.com/blog/post-1', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:35') },
      { id: 7, url: 'https://example.com/blog/post-2', status: 'Failed', statusCode: 404, crawledAt: new Date('2024-12-08 10:36') },
      { id: 8, url: 'https://example.com/products', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:37') },
      { id: 9, url: 'https://example.com/products/item-1', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:38') },
      { id: 10, url: 'https://example.com/products/item-2', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:39') },
      { id: 11, url: 'https://example.com/products/item-3', status: 'Failed', statusCode: 500, crawledAt: new Date('2024-12-08 10:40') },
      { id: 12, url: 'https://example.com/login', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-08 10:41') },
    ],
  },
  {
    id: 2,
    name: 'Blog Platform',
    url: 'https://blog.example.com',
    type: 'Blog',
    urls: [
      { id: 1, url: 'https://blog.example.com/', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-07 09:00') },
      { id: 2, url: 'https://blog.example.com/posts', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-07 09:01') },
      { id: 3, url: 'https://blog.example.com/posts/1', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-07 09:02') },
      { id: 4, url: 'https://blog.example.com/posts/2', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-07 09:03') },
      { id: 5, url: 'https://blog.example.com/author', status: 'Success', statusCode: 200, crawledAt: new Date('2024-12-07 09:04') },
    ],
  },
]

const siteId = parseInt(route.params.siteId as string)
const siteData = computed(() => sitesData.find(s => s.id === siteId))
const siteName = computed(() => siteData.value?.name || 'Unknown Site')
const siteUrl = computed(() => siteData.value?.url || '')
const allUrls = computed(() => siteData.value?.urls || [])

const successCount = computed(() => allUrls.value.filter(u => u.status === 'Success').length)
const failedCount = computed(() => allUrls.value.filter(u => u.status === 'Failed').length)
const totalUrlCount = computed(() => allUrls.value.length)
const totalPages = computed(() => Math.ceil(allUrls.value.length / itemsPerPage))

const paginatedUrls = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage
  return allUrls.value.slice(start, start + itemsPerPage)
})

const getUrlRowClass = (status: StatusType) => status.toLowerCase()
const getUrlStatusClass = (status: StatusType) => status.toLowerCase()

const nextPage = () => { if (currentPage.value < totalPages.value) currentPage.value++ }
const previousPage = () => { if (currentPage.value > 1) currentPage.value-- }

const formatDate = (date: Date) => {
  return new Date(date).toLocaleDateString('en-US', { 
    year: 'numeric', 
    month: 'short', 
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

// ✅ Re-Index button handler (status now allows "Queue")
const reIndex = (urlId: number) => {
  const url = allUrls.value.find(u => u.id === urlId)
  if (url) {
    url.status = 'Queue'
    alert(`URL "${url.url}" queued for re-indexing!`)
  }
}
</script>


<style scoped>
/* Styles mostly same as your previous version with minor adjustments for Actions button */

.page-container { flex: 1; padding: 30px; overflow-y: auto; background: #f9f9f9; }
.page-header { margin-bottom: 30px; }
.back-link { display: inline-block; margin-bottom: 15px; color: #22c55e; text-decoration: none; font-weight: 500; transition: color 0.2s; }
.back-link:hover { color: #16a34a; }
.page-header h1 { font-size: 32px; color: #333; margin: 0 0 10px 0; font-weight: 700; }
.subtitle { font-size: 14px; color: #999; margin: 0; }
.summary-card { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin-bottom: 30px; }
.summary-item { background: #ffffff; padding: 20px; border-radius: 8px; border: 1px solid #e8e8e8; display: flex; flex-direction: column; gap: 8px; }
.summary-item .label { font-size: 12px; color: #999; font-weight: 500; text-transform: uppercase; letter-spacing: 0.5px; }
.summary-item .value { font-size: 24px; color: #333; font-weight: 700; }
.summary-item .value.success { color: #22c55e; }
.summary-item .value.failed { color: #ef4444; }
.table-card { background: #ffffff; border-radius: 12px; border: 1px solid #e8e8e8; overflow: hidden; box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05); }
.urls-table { width: 100%; border-collapse: collapse; }
.urls-table thead { background: #f5f5f5; border-bottom: 1px solid #e8e8e8; }
.urls-table th { padding: 15px; text-align: left; font-weight: 600; font-size: 13px; color: #666; text-transform: uppercase; letter-spacing: 0.5px; }
.urls-table td { padding: 15px; border-bottom: 1px solid #f0f0f0; font-size: 14px; color: #333; }
.urls-table tbody tr.success { background: #fafafa; }
.urls-table tbody tr.failed { background: #fef2f2; }
.url-cell a { color: #22c55e; text-decoration: none; word-break: break-word; }
.url-cell a:hover { text-decoration: underline; }
.status-badge { padding: 6px 12px; border-radius: 20px; font-size: 12px; font-weight: 500; display: inline-block; }
.status-badge.success { background: #e8f5e9; color: #22c55e; }
.status-badge.failed { background: #fef2f2; color: #ef4444; }
.status-code { font-family: monospace; font-weight: 500; }
.pagination { display: flex; justify-content: center; align-items: center; gap: 20px; padding: 20px; background: #f9f9f9; border-top: 1px solid #e8e8e8; }
.pagination-btn { padding: 8px 16px; background: #f5f5f5; border: 1px solid #e8e8e8; border-radius: 6px; color: #666; font-size: 14px; cursor: pointer; transition: all 0.2s; }
.pagination-btn:hover:not(:disabled) { background: #22c55e; border-color: #22c55e; color: #ffffff; }
.pagination-btn:disabled { opacity: 0.5; cursor: not-allowed; }
.pagination-info { font-size: 14px; color: #666; font-weight: 500; }
.action-cell { display: flex; gap: 8px; }
.reindex-btn { padding: 6px 12px; background: #f5f5f5; border: 1px solid #e8e8e8; border-radius: 6px; font-size: 12px; cursor: pointer; transition: all 0.2s; }
.reindex-btn:hover { background: #f97316; border-color: #f97316; color: #ffffff; }
@media (max-width: 768px) { .page-container { padding: 20px; } .summary-card { grid-template-columns: 1fr; } .pagination { flex-direction: column; gap: 10px; } }
</style>
