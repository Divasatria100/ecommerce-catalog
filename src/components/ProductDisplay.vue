<template>
  <div :class="computedThemeClasses" class="showcase-root">
    <div class="visual-backdrop"></div>

    <transition name="fade-content" mode="out-in">
      <Loader v-if="viewState === VIEW_STATES.LOADING" key="loader" />

      <div v-else-if="viewState === VIEW_STATES.PRODUCT_AVAILABLE" key="product" class="content-card content-card--showcase">
        <div class="showcase-grid">
          <div class="grid-column grid-column--media">
            <img :src="productInfo.image" alt="Product Image" class="media-visual" />
          </div>
          
          <div class="grid-column grid-column--info">
            <header class="info-header">
              <h3 class="info-title">{{ productInfo.title }}</h3>
              
              <div class="metadata-bar">
                <span class="metadata-label">{{ productInfo.category }}</span>
                
                <div class="rating-display">
                  <span class="rating-value">{{ productInfo.rating.rate }}/5</span>
                  <div class="rating-stars">
                    <span
                      v-for="position in starPositions"
                      :key="position"
                      class="star-icon"
                      :class="getStarClass(position)"
                    ></span>
                  </div>
                </div>
              </div>
            </header>
            
            <div class="info-body">
              <p class="info-description">{{ productInfo.description }}</p>
            </div>
            
            <footer class="info-footer">
              <h4 class="price-display">${{ productInfo.price }}</h4>
              <nav class="action-controls">
                <button class="control-btn control-btn--purchase" type="button">
                  Buy Now
                </button>
                <button
                  class="control-btn control-btn--navigate"
                  type="button"
                  @click="advanceToNextProduct"
                >
                  Next Product
                </button>
              </nav>
            </footer>
          </div>
        </div>
      </div>

      <div v-else key="unavailable" class="content-card content-card--unavailable">
        <div class="unavailable-content">
          <p class="unavailable-message">This product is unavailable to show</p>
          <button class="control-btn control-btn--fallback" type="button" @click="advanceToNextProduct">
            Next Product
          </button>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import Loader from "./Loader.vue";

const PRODUCT_API_CONFIG = {
  baseUrl: 'https://fakestoreapi.com/products',
  idRange: { min: 1, max: 20 },
  categoryFilter: 'clothing'
};

const VIEW_STATES = Object.freeze({
  LOADING: 'loading',
  PRODUCT_AVAILABLE: 'product_available',
  PRODUCT_UNAVAILABLE: 'product_unavailable'
});

const THEME_CONFIG = {
  women: { primary: 'theme-women', pattern: 'has-pattern' },
  men: { primary: 'theme-men', pattern: 'has-pattern' }
};

export default {
  name: 'ProductShowcase',
  
  components: { Loader },
  
  data() {
    return {
      productSequenceId: PRODUCT_API_CONFIG.idRange.min,
      productInfo: null,
      viewState: VIEW_STATES.LOADING,
      VIEW_STATES,
      starPositions: [1, 2, 3, 4, 5]
    };
  },
  
  computed: {
    computedThemeClasses() {
      return this.productInfo ? this.deriveThemeFromCategory() : [];
    },
    
    calculatedStarRating() {
      return this.productInfo?.rating?.rate 
        ? Math.round(this.productInfo.rating.rate) 
        : 0;
    }
  },
  
  methods: {
    deriveThemeFromCategory() {
      const categoryText = this.productInfo.category.toLowerCase();
      const themeKey = categoryText.includes('women') ? 'women' : 'men';
      const theme = THEME_CONFIG[themeKey];
      return [theme.primary, theme.pattern];
    },
    
    getStarClass(position) {
      return position <= this.calculatedStarRating ? 'star-icon--active' : '';
    },
    
    async retrieveProductFromAPI() {
      this.transitionToLoadingState();
      
      try {
        const productData = await this.executeAPIRequest();
        this.handleSuccessfulFetch(productData);
      } catch (error) {
        this.handleFailedFetch(error);
      }
    },
    
    async executeAPIRequest() {
      const endpoint = this.buildAPIEndpoint();
      const response = await fetch(endpoint);
      
      if (!response.ok) {
        throw new Error(`API responded with status: ${response.status}`);
      }
      
      return await response.json();
    },
    
    buildAPIEndpoint() {
      return `${PRODUCT_API_CONFIG.baseUrl}/${this.productSequenceId}`;
    },
    
    handleSuccessfulFetch(productData) {
      const isValidProduct = this.validateProductCategory(productData);
      
      if (isValidProduct) {
        this.productInfo = productData;
        this.viewState = VIEW_STATES.PRODUCT_AVAILABLE;
      } else {
        this.productInfo = null;
        this.viewState = VIEW_STATES.PRODUCT_UNAVAILABLE;
      }
    },
    
    handleFailedFetch(error) {
      console.error(`Product retrieval failed: ${error.message}`);
      this.productInfo = null;
      this.viewState = VIEW_STATES.PRODUCT_UNAVAILABLE;
    },
    
    validateProductCategory(productData) {
      return productData.category.includes(PRODUCT_API_CONFIG.categoryFilter);
    },
    
    transitionToLoadingState() {
      this.viewState = VIEW_STATES.LOADING;
    },
    
    advanceToNextProduct() {
      this.productSequenceId = this.calculateNextProductId();
      this.retrieveProductFromAPI();
    },
    
    calculateNextProductId() {
      const { min, max } = PRODUCT_API_CONFIG.idRange;
      return this.productSequenceId >= max ? min : this.productSequenceId + 1;
    }
  },
  
  beforeMount() {
    this.retrieveProductFromAPI();
  }
};
</script>

<style scoped>
/* ===== ROOT & BACKDROP ===== */
.showcase-root {
  position: relative;
}

.visual-backdrop {
  height: 548px;
  background-color: var(--neutral-light);
}

.theme-men .visual-backdrop {
  background-color: var(--bg-men);
}

.theme-women .visual-backdrop {
  background-color: var(--bg-women);
}

.has-pattern .visual-backdrop {
  background-image: url(../assets/img/pattern.svg);
  background-repeat: no-repeat;
  background-size: cover;
}

/* ===== TRANSITIONS ===== */
.fade-content-enter-active,
.fade-content-leave-active {
  transition: opacity 0.3s ease;
}

.fade-content-enter-from,
.fade-content-leave-to {
  opacity: 0;
}

/* ===== CONTENT CARD BASE ===== */
.content-card {
  position: absolute;
  top: 125px;
  left: 50%;
  transform: translateX(-50%);
  width: 1034px;
  height: 580px;
  background-color: var(--surface-white);
  border-radius: 10px;
}

/* ===== SHOWCASE CARD ===== */
.content-card--showcase {
  box-shadow: 2px 4px 21px rgba(0, 0, 0, 0.2), 0px 4px 4px rgba(0, 0, 0, 0.25);
  padding: 50px 56px 46px;
}

.showcase-grid {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 68px;
  height: 100%;
}

.grid-column--media {
  flex-shrink: 0;
}

.media-visual {
  width: 300px;
  display: block;
}

.grid-column--info {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex: 1;
  height: 100%;
}

/* ===== INFO HEADER ===== */
.info-header {
  margin-bottom: auto;
}

.info-title {
  font-weight: 600;
  font-size: 28px;
  line-height: 34px;
  margin-bottom: 17px;
}

.theme-men .info-title {
  color: var(--primary-men);
}

.theme-women .info-title {
  color: var(--primary-women);
}

.metadata-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-weight: 400;
  font-size: 18px;
  color: var(--text-secondary);
  border-bottom: 1px solid rgba(0, 0, 0, 0.2);
  padding-bottom: 11px;
  margin-bottom: 21px;
}

.metadata-label {
  /* Inherits styles from metadata-bar */
}

/* ===== RATING DISPLAY ===== */
.rating-display {
  display: flex;
  align-items: center;
  gap: 5px;
}

.rating-value {
  /* Inherits from metadata-bar */
}

.rating-stars {
  display: flex;
  align-items: center;
  gap: 2px;
}

.star-icon {
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background-color: var(--surface-white);
}

.theme-men .star-icon {
  border: 1px solid var(--primary-men);
}

.theme-men .star-icon--active {
  background-color: var(--primary-men);
}

.theme-women .star-icon {
  border: 1px solid var(--primary-women);
}

.theme-women .star-icon--active {
  background-color: var(--primary-women);
}

/* ===== INFO BODY ===== */
.info-body {
  flex: 1;
}

.info-description {
  display: -webkit-box;
  -webkit-line-clamp: 8;
  -webkit-box-orient: vertical;
  font-weight: 400;
  font-size: 20px;
  line-height: 24px;
  color: var(--text-primary);
  overflow: hidden;
}

/* ===== INFO FOOTER ===== */
.info-footer {
  border-top: 1px solid rgba(0, 0, 0, 0.2);
  padding-top: 16px;
  margin-top: 21px;
}

.price-display {
  font-weight: 600;
  font-size: 28px;
  line-height: 34px;
  margin-bottom: 15px;
}

.theme-men .price-display {
  color: var(--primary-men);
}

.theme-women .price-display {
  color: var(--primary-women);
}

/* ===== ACTION CONTROLS ===== */
.action-controls {
  display: flex;
  justify-content: space-between;
}

.control-btn {
  font-weight: 600;
  font-size: 20px;
  line-height: 24px;
  border-radius: 4px;
  cursor: pointer;
  border: none;
  transition: opacity 0.2s ease;
}

.control-btn:hover {
  opacity: 0.9;
}

.control-btn--purchase {
  color: var(--surface-white);
  padding: 9px 88px;
}

.theme-men .control-btn--purchase {
  background-color: var(--primary-men);
}

.theme-women .control-btn--purchase {
  background-color: var(--primary-women);
}

.control-btn--navigate {
  background-color: transparent;
  border: 3px solid;
  padding: 9px 66px;
}

.theme-men .control-btn--navigate {
  color: var(--primary-men);
  border-color: var(--primary-men);
}

.theme-women .control-btn--navigate {
  color: var(--primary-women);
  border-color: var(--primary-women);
}

/* ===== UNAVAILABLE STATE ===== */
.content-card--unavailable {
  display: flex;
  align-items: center;
  justify-content: center;
  background-image: url(../assets/img/sad-face.png);
  background-repeat: no-repeat;
  background-position: 65px center;
  box-shadow: 0 4px 4px rgba(0, 0, 0, 0.25);
}

.unavailable-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 13px;
}

.unavailable-message {
  font-weight: 400;
  font-size: 20px;
  line-height: 24px;
}

.control-btn--fallback {
  color: var(--text-secondary);
  background-color: transparent;
  border: 3px solid #000;
  padding: 9px 169px;
}
</style>
