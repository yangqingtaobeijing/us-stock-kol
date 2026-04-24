<template>
  <div class="min-h-screen bg-[#0F172A] text-slate-200 font-sans">
    <!-- 顶部导航 -->
    <header class="fixed top-0 left-0 right-0 z-50 bg-[#0F172A]/95 backdrop-blur border-b border-slate-800">
      <div class="max-w-[1400px] mx-auto px-8 h-16 flex items-center justify-between">
        <div class="flex items-center gap-3">
          <span class="text-2xl">📈</span>
          <div>
            <h1 class="text-lg font-bold text-white leading-none">美股 KOL 导航</h1>
            <p class="text-xs text-slate-500 mt-0.5">精选国内外美股投资人与博主</p>
          </div>
        </div>
        <div class="flex items-center gap-6 text-sm text-slate-400">
          <span><span class="text-amber-400 font-bold text-base">{{ filtered.length }}</span> 位 KOL</span>
          <span class="text-slate-600">|</span>
          <span>国外 <span class="text-amber-400 font-semibold">{{ foreignCount }}</span></span>
          <span>国内 <span class="text-amber-400 font-semibold">{{ domesticCount }}</span></span>
        </div>
      </div>
    </header>

    <!-- 筛选区 -->
    <div class="sticky top-16 z-40 bg-[#0F172A]/95 backdrop-blur border-b border-slate-800/60">
      <div class="max-w-[1400px] mx-auto px-8 py-4 flex flex-wrap items-center gap-4">
        <!-- 搜索 -->
        <div class="relative flex-1 min-w-[200px] max-w-xs">
          <span class="absolute left-3 top-1/2 -translate-y-1/2 text-slate-500 text-sm">🔍</span>
          <input
            v-model="search"
            type="text"
            placeholder="搜索名字、观点..."
            class="w-full bg-slate-800 border border-slate-700 rounded-lg pl-9 pr-4 py-2 text-sm text-slate-200 placeholder-slate-500 focus:outline-none focus:border-amber-500 transition"
          />
        </div>
        <!-- 地区 -->
        <div class="flex items-center gap-2">
          <span class="text-xs text-slate-500">地区</span>
          <div class="flex gap-1">
            <button
              v-for="r in regions"
              :key="r"
              @click="regionFilter = r"
              :class="[
                'px-3 py-1.5 rounded-lg text-xs font-medium transition',
                regionFilter === r
                  ? 'bg-amber-500 text-black'
                  : 'bg-slate-800 text-slate-400 hover:bg-slate-700'
              ]"
            >{{ r }}</button>
          </div>
        </div>
        <!-- 类型 -->
        <div class="flex items-center gap-2 flex-wrap">
          <span class="text-xs text-slate-500">类型</span>
          <div class="flex gap-1 flex-wrap">
            <button
              v-for="t in types"
              :key="t"
              @click="typeFilter = t"
              :class="[
                'px-3 py-1.5 rounded-lg text-xs font-medium transition',
                typeFilter === t
                  ? 'bg-amber-500 text-black'
                  : 'bg-slate-800 text-slate-400 hover:bg-slate-700'
              ]"
            >{{ t }}</button>
          </div>
        </div>
      </div>
    </div>

    <!-- 卡片网格 -->
    <main class="max-w-[1400px] mx-auto px-8 py-8 pt-6">
      <div v-if="filtered.length === 0" class="text-center py-24 text-slate-500">
        没有找到匹配的 KOL
      </div>
      <div v-else class="grid grid-cols-3 gap-6">
        <div
          v-for="kol in filtered"
          :key="kol.id"
          class="bg-[#1E293B] rounded-xl border border-slate-700/60 hover:border-amber-500/40 transition-all duration-200 overflow-hidden group"
        >
          <!-- 卡片头部 -->
          <div class="p-5 pb-4">
            <div class="flex items-start justify-between gap-3 mb-3">
              <div>
                <h2 class="text-lg font-bold text-white group-hover:text-amber-400 transition">{{ kol.name }}</h2>
                <p v-if="kol.nameEn" class="text-xs text-slate-500 mt-0.5">{{ kol.nameEn }}</p>
              </div>
              <div class="flex flex-col items-end gap-1 shrink-0">
                <span :class="[
                  'px-2 py-0.5 rounded text-xs font-medium',
                  kol.region === '国外' ? 'bg-blue-500/20 text-blue-400' : 'bg-green-500/20 text-green-400'
                ]">{{ kol.region }}</span>
                <span class="px-2 py-0.5 rounded text-xs bg-amber-500/10 text-amber-400">{{ kol.type }}</span>
              </div>
            </div>
            <p class="text-sm text-slate-400 leading-relaxed">{{ kol.bio }}</p>

            <!-- 标签 -->
            <div class="flex flex-wrap gap-1.5 mt-3">
              <span
                v-for="tag in kol.tags"
                :key="tag"
                class="text-xs px-2 py-0.5 bg-slate-700/60 text-slate-400 rounded"
              >{{ tag }}</span>
            </div>
          </div>

          <!-- 社交媒体 -->
          <div class="px-5 pb-4 flex flex-wrap gap-2">
            <a v-if="kol.twitter" :href="kol.twitter" target="_blank" rel="noopener"
              class="flex items-center gap-1.5 px-3 py-1.5 bg-slate-700/60 hover:bg-[#1DA1F2]/20 hover:text-[#1DA1F2] rounded-lg text-xs text-slate-400 transition">
              <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="currentColor"><path d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-4.714-6.231-5.401 6.231H2.744l7.73-8.835L1.254 2.25H8.08l4.264 5.633 5.9-5.633zm-1.161 17.52h1.833L7.084 4.126H5.117z"/></svg>
              Twitter
            </a>
            <a v-if="kol.youtube" :href="kol.youtube" target="_blank" rel="noopener"
              class="flex items-center gap-1.5 px-3 py-1.5 bg-slate-700/60 hover:bg-red-500/20 hover:text-red-400 rounded-lg text-xs text-slate-400 transition">
              <svg class="w-3.5 h-3.5" viewBox="0 0 24 24" fill="currentColor"><path d="M23.498 6.186a3.016 3.016 0 0 0-2.122-2.136C19.505 3.545 12 3.545 12 3.545s-7.505 0-9.377.505A3.017 3.017 0 0 0 .502 6.186C0 8.07 0 12 0 12s0 3.93.502 5.814a3.016 3.016 0 0 0 2.122 2.136c1.871.505 9.376.505 9.376.505s7.505 0 9.377-.505a3.015 3.015 0 0 0 2.122-2.136C24 15.93 24 12 24 12s0-3.93-.502-5.814zM9.545 15.568V8.432L15.818 12l-6.273 3.568z"/></svg>
              YouTube
            </a>
            <a v-if="kol.weibo" :href="kol.weibo" target="_blank" rel="noopener"
              class="flex items-center gap-1.5 px-3 py-1.5 bg-slate-700/60 hover:bg-red-400/20 hover:text-red-300 rounded-lg text-xs text-slate-400 transition">
              🌐 微博
            </a>
            <a v-if="kol.bilibili" :href="kol.bilibili" target="_blank" rel="noopener"
              class="flex items-center gap-1.5 px-3 py-1.5 bg-slate-700/60 hover:bg-cyan-400/20 hover:text-cyan-400 rounded-lg text-xs text-slate-400 transition">
              📺 B站
            </a>
            <a v-if="kol.blog" :href="kol.blog" target="_blank" rel="noopener"
              class="flex items-center gap-1.5 px-3 py-1.5 bg-slate-700/60 hover:bg-amber-400/20 hover:text-amber-300 rounded-lg text-xs text-slate-400 transition">
              🔗 网站
            </a>
            <span v-if="kol.wechat" class="flex items-center gap-1.5 px-3 py-1.5 bg-slate-700/60 text-slate-400 rounded-lg text-xs cursor-default" :title="'公众号：' + kol.wechat">
              💬 {{ kol.wechat }}
            </span>
          </div>

          <!-- 核心观点（折叠） -->
          <div class="border-t border-slate-700/60">
            <button
              @click="toggleExpanded(kol.id)"
              class="w-full px-5 py-3 flex items-center justify-between text-xs text-slate-500 hover:text-slate-300 transition"
            >
              <span class="flex items-center gap-1.5">
                <span>💡</span> 核心观点
              </span>
              <span class="transition-transform duration-200" :class="{ 'rotate-180': expanded.has(kol.id) }">▾</span>
            </button>
            <div v-show="expanded.has(kol.id)" class="px-5 pb-4 space-y-2">
              <div v-for="(view, i) in kol.keyViews" :key="i"
                class="flex gap-2 text-sm text-slate-300">
                <span class="text-amber-500 shrink-0 mt-0.5">{{ i + 1 }}.</span>
                <span>{{ view }}</span>
              </div>
            </div>
          </div>

          <!-- 代表作 -->
          <div v-if="kol.masterpieces?.length" class="border-t border-slate-700/60 px-5 py-4">
            <p class="text-xs text-slate-500 mb-2 flex items-center gap-1.5"><span>📚</span> 代表作</p>
            <div class="space-y-1.5">
              <div v-for="mp in kol.masterpieces.slice(0, 3)" :key="mp.title" class="flex items-start gap-2 text-xs">
                <span class="text-slate-600 shrink-0 mt-0.5">—</span>
                <a v-if="mp.url" :href="mp.url" target="_blank" rel="noopener"
                  class="text-slate-400 hover:text-amber-400 transition">
                  {{ mp.title }}
                  <span class="text-slate-600 ml-1">[{{ mp.type }}]</span>
                </a>
                <span v-else class="text-slate-400">
                  {{ mp.title }}
                  <span class="text-slate-600 ml-1">[{{ mp.type }}]</span>
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- 页脚 -->
    <footer class="border-t border-slate-800 mt-8 py-6 text-center text-xs text-slate-600">
      美股 KOL 导航 · 数据仅供参考，不构成投资建议
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue'
import type { KOL } from './types'

const kols = ref<KOL[]>([])
const search = ref('')
const regionFilter = ref('全部')
const typeFilter = ref('全部')
const expanded = ref(new Set<string>())

const regions = ['全部', '国外', '国内']
const types = ['全部', '投资大师', '价值投资', '宏观分析', '财经媒体', '博主UP主', '量化技术']

onMounted(async () => {
  try {
    const base = import.meta.env.BASE_URL
    const res = await fetch(`${base}data/kol_data.json`)
    kols.value = await res.json()
  } catch (e) {
    console.error('加载数据失败', e)
  }
})

const filtered = computed(() => {
  return kols.value.filter(k => {
    if (regionFilter.value !== '全部' && k.region !== regionFilter.value) return false
    if (typeFilter.value !== '全部' && k.type !== typeFilter.value) return false
    if (search.value) {
      const q = search.value.toLowerCase()
      const text = [k.name, k.nameEn, k.bio, ...k.keyViews, ...k.tags].join(' ').toLowerCase()
      if (!text.includes(q)) return false
    }
    return true
  })
})

const foreignCount = computed(() => kols.value.filter(k => k.region === '国外').length)
const domesticCount = computed(() => kols.value.filter(k => k.region === '国内').length)

function toggleExpanded(id: string) {
  if (expanded.value.has(id)) {
    expanded.value.delete(id)
  } else {
    expanded.value.add(id)
  }
}
</script>
