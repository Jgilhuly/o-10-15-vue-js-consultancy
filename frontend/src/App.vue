<template>
  <div id="app" :class="{ 'dark-mode': isDarkMode }">
    <!-- Navigation -->
    <nav class="nav">
      <div class="container">
        <div class="nav-content">
          <div class="nav-brand">Area</div>
          <div class="nav-links">
            <a href="#home">Home</a>
            <a href="#services">Services</a>
            <a href="#quiz">AI Readiness</a>
            <a href="#calculator">Calculator</a>
            <a href="#team">Team</a>
            <a href="#contact">Contact</a>
            <button @click="toggleDarkMode" class="dark-mode-toggle" :aria-label="isDarkMode ? 'Switch to light mode' : 'Switch to dark mode'">
              <span v-if="isDarkMode">☀️</span>
              <span v-else>🌙</span>
            </button>
          </div>
        </div>
      </div>
    </nav>

    <!-- Hero Section -->
    <section id="home" class="hero">
      <div class="container">
        <div class="text-center">
          <h1 class="text-5xl font-bold text-gray-900 mb-6">
            Transform Your Business with 
            <span class="text-primary">AI Solutions</span>
          </h1>
          <p class="text-xl text-gray-600 mb-8 mx-auto">
            We help enterprises unlock the power of artificial intelligence through strategic consulting, 
            custom AI development, and intelligent automation solutions.
          </p>
          <div class="hero-buttons">
            <button class="btn btn-primary" @click="openContactModal">Get Started</button>
            <button class="btn btn-secondary">Learn More</button>
          </div>
        </div>
      </div>
    </section>

    <!-- Services Section -->
    <section id="services" class="py-20 bg-white">
      <div class="container">
        <div class="text-center mb-16">
          <h2 class="text-4xl font-bold text-gray-900 mb-4">Our AI Services</h2>
          <p class="text-xl text-gray-600">Comprehensive AI solutions tailored to your business needs</p>
        </div>
        
        <div class="grid grid-3">
          <div 
            v-for="service in services" 
            :key="service.id"
            class="card hover-shadow"
          >
            <div class="text-4xl mb-4">{{ service.icon }}</div>
            <h3 class="text-xl font-semibold text-gray-900 mb-3">{{ service.title }}</h3>
            <p class="text-gray-600">{{ service.description }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- Quote Calculator Section -->
    <section id="calculator" class="py-20 bg-gray-50">
      <div class="container">
        <div class="text-center mb-16">
          <h2 class="text-4xl font-bold text-gray-900 mb-4">Service & Quote Calculator</h2>
          <p class="text-xl text-gray-600">Get an instant estimate for your AI project</p>
        </div>
        
        <div class="calculator-container">
          <div class="calculator-grid">
            <!-- Service Selection -->
            <div class="calculator-section">
              <h3 class="calculator-section-title">Select Services</h3>
              <div class="service-checkboxes">
                <label 
                  v-for="service in services" 
                  :key="service.id"
                  class="service-checkbox-label"
                >
                  <input 
                    type="checkbox" 
                    :value="service.id"
                    v-model="calculator.selectedServices"
                    @change="calculateEstimate"
                  />
                  <div class="service-checkbox-content">
                    <div class="service-checkbox-header">
                      <span class="service-icon">{{ service.icon }}</span>
                      <span class="service-name">{{ service.title }}</span>
                    </div>
                    <span class="service-base-price">Base: {{ service.price }}</span>
                  </div>
                </label>
              </div>
            </div>

            <!-- Project Details -->
            <div class="calculator-section">
              <h3 class="calculator-section-title">Project Details</h3>
              
              <div class="form-group">
                <label for="projectScope">Project Scope</label>
                <select 
                  id="projectScope" 
                  v-model="calculator.projectScope"
                  @change="calculateEstimate"
                >
                  <option value="small">Small (1-2 months)</option>
                  <option value="medium">Medium (3-6 months)</option>
                  <option value="large">Large (6+ months)</option>
                </select>
              </div>

              <div class="form-group">
                <label for="teamSize">Estimated Team Size</label>
                <select 
                  id="teamSize" 
                  v-model="calculator.teamSize"
                  @change="calculateEstimate"
                >
                  <option value="1-2">1-2 specialists</option>
                  <option value="3-5">3-5 specialists</option>
                  <option value="6+">6+ specialists</option>
                </select>
              </div>

              <div class="form-group">
                <label for="urgency">Project Urgency</label>
                <select 
                  id="urgency" 
                  v-model="calculator.urgency"
                  @change="calculateEstimate"
                >
                  <option value="standard">Standard Timeline</option>
                  <option value="expedited">Expedited (1.2x)</option>
                  <option value="rush">Rush (1.5x)</option>
                </select>
              </div>

              <div class="form-group">
                <label>
                  <input 
                    type="checkbox" 
                    v-model="calculator.includeSupport"
                    @change="calculateEstimate"
                  />
                  Include 6-month post-launch support (+15%)
                </label>
              </div>

              <div class="form-group">
                <label>
                  <input 
                    type="checkbox" 
                    v-model="calculator.includeTraining"
                    @change="calculateEstimate"
                  />
                  Include team training (+10%)
                </label>
              </div>
            </div>
          </div>

          <!-- Estimate Display -->
          <div class="estimate-card">
            <div class="estimate-header">
              <h3>Your Estimate</h3>
              <div class="estimate-amount">${{ formatNumber(calculator.totalEstimate) }}</div>
            </div>
            
            <div class="estimate-breakdown" v-if="calculator.selectedServices.length > 0">
              <div class="breakdown-item">
                <span>Base Services</span>
                <span>${{ formatNumber(calculator.basePrice) }}</span>
              </div>
              <div class="breakdown-item" v-if="calculator.scopeMultiplier > 1">
                <span>Project Scope Adjustment</span>
                <span>×{{ calculator.scopeMultiplier }}</span>
              </div>
              <div class="breakdown-item" v-if="calculator.urgencyMultiplier > 1">
                <span>Urgency Adjustment</span>
                <span>×{{ calculator.urgencyMultiplier }}</span>
              </div>
              <div class="breakdown-item" v-if="calculator.includeSupport">
                <span>Post-Launch Support</span>
                <span>+15%</span>
              </div>
              <div class="breakdown-item" v-if="calculator.includeTraining">
                <span>Team Training</span>
                <span>+10%</span>
              </div>
            </div>

            <button 
              class="btn btn-primary btn-full"
              @click="openQuoteModal"
              :disabled="calculator.selectedServices.length === 0"
            >
              Request Detailed Quote
            </button>
            
            <p class="estimate-note">
              This is a preliminary estimate. Final pricing will be determined after a detailed consultation.
            </p>
          </div>
        </div>
      </div>
    </section>

    <!-- AI Readiness Quiz Section -->
    <section id="quiz" class="py-20 bg-white">
      <div class="container">
        <div class="text-center mb-16">
          <h2 class="text-4xl font-bold text-gray-900 mb-4">AI Readiness Quiz</h2>
          <p class="text-xl text-gray-600">Answer a few questions to gauge your organization's AI readiness</p>
        </div>

        <div class="grid grid-3">
          <div class="card" style="grid-column: 1 / -1;">
            <div v-for="q in quizQuestions" :key="q.id" class="form-group">
              <label :for="`q-${q.id}`">{{ q.text }}</label>
              <div class="quiz-scale" :aria-labelledby="`q-${q.id}`">
                <label v-for="n in 5" :key="n" style="margin-right: 0.75rem; display: inline-flex; align-items: center; gap: 0.375rem;">
                  <input type="radio" :name="`q-${q.id}`" :value="n" v-model.number="quizAnswers[q.id]" />
                  <span>{{ n }}</span>
                </label>
                <div class="text-sm text-gray-600" style="margin-top: 0.25rem;">1 = Needs work, 5 = Excellent</div>
              </div>
            </div>

            <div class="form-actions" style="justify-content: space-between;">
              <button class="btn btn-secondary" @click="resetQuiz" :disabled="isSubmitting">Reset</button>
              <button class="btn btn-primary" @click="calculateQuiz" :disabled="!isQuizComplete">Calculate Readiness</button>
            </div>
          </div>

          <div v-if="quizSubmitted" class="card" style="grid-column: 1 / -1;">
            <div class="estimate-header" style="margin-bottom: 1rem;">
              <h3>Your AI Readiness</h3>
              <div class="estimate-amount">{{ quizScore }}/{{ maxQuizScore }} ({{ quizLevel }})</div>
            </div>
            <p class="text-gray-600" style="margin-bottom: 1rem;">{{ quizSummary }}</p>

            <div v-if="focusAreas.length" class="mb-6">
              <h4 class="text-lg font-semibold text-gray-900 mb-2">Suggested focus areas</h4>
              <ul>
                <li v-for="area in focusAreas" :key="area" class="text-gray-600">- {{ area }}</li>
              </ul>
            </div>

            <div class="form-actions" style="justify-content: flex-end;">
              <button class="btn btn-secondary" @click="resetQuiz">Retake Quiz</button>
              <button class="btn btn-primary" @click="contactFromQuiz">Talk to an Expert</button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Team Section -->
    <section id="team" class="py-20 bg-white">
      <div class="container">
        <div class="text-center mb-16">
          <h2 class="text-4xl font-bold text-gray-900 mb-4">Meet Our Team</h2>
          <p class="text-xl text-gray-600">AI experts with deep industry experience</p>
        </div>
        
        <div class="grid grid-3">
          <div 
            v-for="member in team" 
            :key="member.id"
            class="card-white hover-shadow"
          >
            <div class="team-avatar">
              <span>{{ member.initials }}</span>
            </div>
            <h3 class="text-xl font-semibold text-gray-900 text-center mb-2">{{ member.name }}</h3>
            <p class="text-primary text-center mb-3">{{ member.role }}</p>
            <p class="text-gray-600 text-center text-sm">{{ member.bio }}</p>
          </div>
        </div>
      </div>
    </section>

    <!-- Contact Section -->
    <section id="contact" class="py-20 bg-gray-50">
      <div class="container">
        <div class="text-center mb-16">
          <h2 class="text-4xl font-bold text-gray-900 mb-4">Get In Touch</h2>
          <p class="text-xl text-gray-600">Ready to transform your business with AI?</p>
        </div>
        
        <div class="grid grid-3">
          <div class="contact-item">
            <div class="contact-icon">📧</div>
            <h3 class="text-lg font-semibold text-gray-900 mb-2">Email</h3>
            <p class="text-gray-600">{{ contactInfo.email }}</p>
          </div>
          <div class="contact-item">
            <div class="contact-icon">📞</div>
            <h3 class="text-lg font-semibold text-gray-900 mb-2">Phone</h3>
            <p class="text-gray-600">{{ contactInfo.phone }}</p>
          </div>
          <div class="contact-item">
            <div class="contact-icon">📍</div>
            <h3 class="text-lg font-semibold text-gray-900 mb-2">Location</h3>
            <p class="text-gray-600">{{ contactInfo.location }}</p>
          </div>
        </div>
        
        <div class="text-center mt-12">
          <button class="btn btn-primary" @click="openContactModal">Contact Us</button>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="footer">
      <div class="container text-center">
        <div class="text-2xl font-bold mb-4">Area</div>
        <p class="text-gray-400">© 2025 Area. All rights reserved.</p>
      </div>
    </footer>

    <!-- Contact Modal -->
    <div v-if="showContactModal" class="modal-overlay" @click="closeContactModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h3 class="text-2xl font-bold text-gray-900">Contact Us</h3>
          <button class="modal-close" @click="closeContactModal">&times;</button>
        </div>
        
        <form @submit.prevent="submitContactForm" class="contact-form">
          <div class="form-group">
            <label for="name">Full Name *</label>
            <input 
              type="text" 
              id="name" 
              v-model="contactForm.name" 
              required 
              placeholder="Enter your full name"
            />
          </div>
          
          <div class="form-group">
            <label for="email">Email Address *</label>
            <input 
              type="email" 
              id="email" 
              v-model="contactForm.email" 
              required 
              placeholder="Enter your email address"
            />
          </div>
          
          <div class="form-group">
            <label for="company">Company</label>
            <input 
              type="text" 
              id="company" 
              v-model="contactForm.company" 
              placeholder="Enter your company name"
            />
          </div>
          
          <div class="form-group">
            <label for="phone">Phone Number</label>
            <input 
              type="tel" 
              id="phone" 
              v-model="contactForm.phone" 
              placeholder="Enter your phone number"
            />
          </div>
          
          <div class="form-group">
            <label for="service">Service Interest</label>
            <select id="service" v-model="contactForm.service">
              <option value="">Select a service</option>
              <option value="ai-strategy">AI Strategy Consulting</option>
              <option value="ml-development">Machine Learning Development</option>
              <option value="automation">Process Automation</option>
              <option value="other">Other</option>
            </select>
          </div>
          
          <div class="form-group">
            <label for="message">Message *</label>
            <textarea 
              id="message" 
              v-model="contactForm.message" 
              required 
              rows="4"
              placeholder="Tell us about your project or how we can help"
            ></textarea>
          </div>
          
          <div class="form-actions">
            <button type="button" class="btn btn-secondary" @click="closeContactModal">Cancel</button>
            <button type="submit" class="btn btn-primary" :disabled="isSubmitting">
              {{ isSubmitting ? 'Sending...' : 'Send Message' }}
            </button>
          </div>
        </form>
      </div>
    </div>

    <!-- Quote Request Modal -->
    <div v-if="showQuoteModal" class="modal-overlay" @click="closeQuoteModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h3 class="text-2xl font-bold text-gray-900">Request Detailed Quote</h3>
          <button class="modal-close" @click="closeQuoteModal">&times;</button>
        </div>
        
        <form @submit.prevent="submitQuoteRequest" class="contact-form">
          <div class="quote-summary">
            <h4>Your Selection</h4>
            <div class="selected-services-list">
              <div v-for="serviceId in calculator.selectedServices" :key="serviceId">
                {{ getServiceName(serviceId) }}
              </div>
            </div>
            <div class="quote-summary-total">
              Estimated Total: ${{ formatNumber(calculator.totalEstimate) }}
            </div>
          </div>

          <div class="form-group">
            <label for="quoteName">Full Name *</label>
            <input 
              type="text" 
              id="quoteName" 
              v-model="quoteForm.name" 
              required 
              placeholder="Enter your full name"
            />
          </div>
          
          <div class="form-group">
            <label for="quoteEmail">Email Address *</label>
            <input 
              type="email" 
              id="quoteEmail" 
              v-model="quoteForm.email" 
              required 
              placeholder="Enter your email address"
            />
          </div>
          
          <div class="form-group">
            <label for="quoteCompany">Company *</label>
            <input 
              type="text" 
              id="quoteCompany" 
              v-model="quoteForm.company" 
              required 
              placeholder="Enter your company name"
            />
          </div>
          
          <div class="form-group">
            <label for="quotePhone">Phone Number</label>
            <input 
              type="tel" 
              id="quotePhone" 
              v-model="quoteForm.phone" 
              placeholder="Enter your phone number"
            />
          </div>
          
          <div class="form-group">
            <label for="quoteMessage">Project Details</label>
            <textarea 
              id="quoteMessage" 
              v-model="quoteForm.message" 
              rows="4"
              placeholder="Tell us more about your project requirements"
            ></textarea>
          </div>
          
          <div class="form-actions">
            <button type="button" class="btn btn-secondary" @click="closeQuoteModal">Cancel</button>
            <button type="submit" class="btn btn-primary" :disabled="isSubmittingQuote">
              {{ isSubmittingQuote ? 'Sending...' : 'Request Quote' }}
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import axios from 'axios'

export default {
  name: 'App',
  setup() {
    const isDarkMode = ref(false)
    const services = ref([])
    const team = ref([])
    const contactInfo = ref({})
    const showContactModal = ref(false)
    const showQuoteModal = ref(false)
    const isSubmitting = ref(false)
    const isSubmittingQuote = ref(false)
    const contactForm = ref({
      name: '',
      email: '',
      company: '',
      phone: '',
      service: '',
      message: ''
    })
    const quoteForm = ref({
      name: '',
      email: '',
      company: '',
      phone: '',
      message: ''
    })
    const calculator = ref({
      selectedServices: [],
      projectScope: 'medium',
      teamSize: '1-2',
      urgency: 'standard',
      includeSupport: false,
      includeTraining: false,
      basePrice: 0,
      scopeMultiplier: 1,
      urgencyMultiplier: 1,
      totalEstimate: 0
    })

    // AI Readiness Quiz state
    const quizQuestions = ref([
      { id: 1, text: 'We have a clear AI strategy aligned to business goals.' },
      { id: 2, text: 'Our data is accessible, high quality, and well-governed.' },
      { id: 3, text: 'We have the skills (or partners) to deliver AI projects.' },
      { id: 4, text: 'Our technology stack supports ML lifecycle (MLOps, CI/CD, monitoring).' },
      { id: 5, text: 'We have processes for AI governance, privacy, and risk management.' },
      { id: 6, text: 'We have prioritized, high-ROI AI use cases with defined owners.' }
    ])
    const quizAnswers = ref({})
    const quizSubmitted = ref(false)
    const quizScore = ref(0)
    const maxQuizScore = computed(() => quizQuestions.value.length * 5)
    const isQuizComplete = computed(() => {
      return quizQuestions.value.every(q => Number.isInteger(quizAnswers.value[q.id]))
    })
    const quizLevel = computed(() => {
      const pct = maxQuizScore.value === 0 ? 0 : quizScore.value / maxQuizScore.value
      if (pct >= 0.85) return 'Leading'
      if (pct >= 0.65) return 'Ready'
      if (pct >= 0.45) return 'Progressing'
      return 'Emerging'
    })
    const focusAreas = ref([])
    const quizSummary = ref('')

    const calculateQuiz = () => {
      let total = 0
      const lowAreas = []
      quizQuestions.value.forEach(q => {
        const val = Number(quizAnswers.value[q.id] || 0)
        total += val
        if (val <= 3) {
          // Map question to focus area
          switch (q.id) {
            case 1: lowAreas.push('Define an AI strategy with executive sponsorship'); break
            case 2: lowAreas.push('Improve data quality, accessibility, and governance'); break
            case 3: lowAreas.push('Augment skills via hiring, training, or a partner'); break
            case 4: lowAreas.push('Strengthen MLOps and production infrastructure'); break
            case 5: lowAreas.push('Establish AI governance and risk controls'); break
            case 6: lowAreas.push('Prioritize use cases with owners and ROI'); break
          }
        }
      })
      quizScore.value = total
      focusAreas.value = Array.from(new Set(lowAreas))
      quizSummary.value = `Your AI readiness level is ${quizLevel.value}. Score ${quizScore.value}/${maxQuizScore.value}.`
      quizSubmitted.value = true
    }

    const resetQuiz = () => {
      quizAnswers.value = {}
      quizSubmitted.value = false
      quizScore.value = 0
      focusAreas.value = []
      quizSummary.value = ''
    }

    const contactFromQuiz = () => {
      const summaryLines = [
        `AI Readiness: ${quizLevel.value} (${quizScore.value}/${maxQuizScore.value})`,
        focusAreas.value.length ? `Focus areas: ${focusAreas.value.join('; ')}` : ''
      ].filter(Boolean)
      contactForm.value.service = 'ai-strategy'
      contactForm.value.message = `Hi Area team,\n\nI just completed the AI Readiness Quiz.\n${summaryLines.join('\n')}\n\nI’d like help with a tailored roadmap and next steps.`
      openContactModal()
    }

    const openContactModal = () => {
      showContactModal.value = true
      document.body.style.overflow = 'hidden'
    }

    const closeContactModal = () => {
      showContactModal.value = false
      document.body.style.overflow = 'auto'
      // Reset form
      contactForm.value = {
        name: '',
        email: '',
        company: '',
        phone: '',
        service: '',
        message: ''
      }
    }

    const submitContactForm = async () => {
      isSubmitting.value = true
      try {
        // Send the form data to the backend
        const response = await axios.post('/api/contact/consultation', {
          name: contactForm.value.name,
          email: contactForm.value.email,
          company: contactForm.value.company,
          message: contactForm.value.message,
          serviceInterest: contactForm.value.service || 'General inquiry'
        })
        
        // Show success message
        alert('Thank you for your message! We\'ll get back to you within 24 hours.')
        
        closeContactModal()
      } catch (error) {
        console.error('Error submitting form:', error)
        if (error.response?.data?.message) {
          alert(`Error: ${error.response.data.message}`)
        } else {
          alert('There was an error sending your message. Please try again.')
        }
      } finally {
        isSubmitting.value = false
      }
    }

    // Calculator functions
    const calculateEstimate = () => {
      // Calculate base price from selected services
      let basePrice = 0
      calculator.value.selectedServices.forEach(serviceId => {
        const service = services.value.find(s => s.id === serviceId)
        if (service && service.price) {
          // Extract numeric value from price string (e.g., "Starting at $15,000" -> 15000)
          const priceMatch = service.price.match(/\$?([\d,]+)/)
          if (priceMatch) {
            basePrice += parseInt(priceMatch[1].replace(/,/g, ''))
          }
        }
      })
      
      calculator.value.basePrice = basePrice
      
      // Apply scope multiplier
      const scopeMultipliers = {
        small: 1,
        medium: 1.5,
        large: 2.5
      }
      calculator.value.scopeMultiplier = scopeMultipliers[calculator.value.projectScope]
      
      // Apply urgency multiplier
      const urgencyMultipliers = {
        standard: 1,
        expedited: 1.2,
        rush: 1.5
      }
      calculator.value.urgencyMultiplier = urgencyMultipliers[calculator.value.urgency]
      
      // Calculate total
      let total = basePrice * calculator.value.scopeMultiplier * calculator.value.urgencyMultiplier
      
      // Add support and training percentages
      if (calculator.value.includeSupport) {
        total *= 1.15
      }
      if (calculator.value.includeTraining) {
        total *= 1.10
      }
      
      calculator.value.totalEstimate = Math.round(total)
    }

    const openQuoteModal = () => {
      showQuoteModal.value = true
      document.body.style.overflow = 'hidden'
    }

    const closeQuoteModal = () => {
      showQuoteModal.value = false
      document.body.style.overflow = 'auto'
      // Reset form
      quoteForm.value = {
        name: '',
        email: '',
        company: '',
        phone: '',
        message: ''
      }
    }

    const submitQuoteRequest = async () => {
      isSubmittingQuote.value = true
      try {
        const selectedServiceNames = calculator.value.selectedServices.map(id => {
          const service = services.value.find(s => s.id === id)
          return service ? service.title : ''
        }).filter(Boolean)

        // Send the quote request to the backend
        const response = await axios.post('/api/contact/quote', {
          name: quoteForm.value.name,
          email: quoteForm.value.email,
          company: quoteForm.value.company,
          phone: quoteForm.value.phone,
          message: quoteForm.value.message,
          selectedServices: selectedServiceNames,
          projectScope: calculator.value.projectScope,
          teamSize: calculator.value.teamSize,
          urgency: calculator.value.urgency,
          includeSupport: calculator.value.includeSupport,
          includeTraining: calculator.value.includeTraining,
          estimatedTotal: calculator.value.totalEstimate
        })
        
        // Show success message
        alert('Thank you for your quote request! Our team will review your requirements and get back to you within 24 hours with a detailed proposal.')
        
        closeQuoteModal()
      } catch (error) {
        console.error('Error submitting quote request:', error)
        if (error.response?.data?.message) {
          alert(`Error: ${error.response.data.message}`)
        } else {
          alert('There was an error sending your quote request. Please try again.')
        }
      } finally {
        isSubmittingQuote.value = false
      }
    }

    const getServiceName = (serviceId) => {
      const service = services.value.find(s => s.id === serviceId)
      return service ? service.title : ''
    }

    const formatNumber = (num) => {
      return num.toLocaleString('en-US')
    }

    const toggleDarkMode = () => {
      isDarkMode.value = !isDarkMode.value
      localStorage.setItem('darkMode', isDarkMode.value.toString())
    }

    const initDarkMode = () => {
      const savedMode = localStorage.getItem('darkMode')
      if (savedMode !== null) {
        isDarkMode.value = savedMode === 'true'
      } else {
        // Check system preference
        isDarkMode.value = window.matchMedia('(prefers-color-scheme: dark)').matches
      }
    }

    const fetchData = async () => {
      try {
        const [servicesRes, teamRes, contactRes] = await Promise.all([
          axios.get('/api/services'),
          axios.get('/api/team'),
          axios.get('/api/contact')
        ])
        
        services.value = servicesRes.data
        team.value = teamRes.data
        contactInfo.value = contactRes.data
      } catch (error) {
        console.error('Error fetching data:', error)
        // Fallback data in case API is not available
        services.value = [
          {
            id: 1,
            title: "AI Strategy Consulting",
            description: "Strategic roadmaps for AI adoption and digital transformation",
            icon: "🎯"
          },
          {
            id: 2,
            title: "Machine Learning Development",
            description: "Custom ML models and algorithms for your specific use cases",
            icon: "🤖"
          },
          {
            id: 3,
            title: "Process Automation",
            description: "Intelligent automation solutions to optimize your workflows",
            icon: "⚡"
          }
        ]
        
        team.value = [
          {
            id: 1,
            name: "Dr. Sarah Chen",
            role: "Chief AI Officer",
            bio: "PhD in Machine Learning, 10+ years in enterprise AI solutions",
            initials: "SC"
          },
          {
            id: 2,
            name: "Michael Rodriguez",
            role: "Lead Data Scientist",
            bio: "Expert in deep learning and computer vision applications",
            initials: "MR"
          },
          {
            id: 3,
            name: "Emily Johnson",
            role: "AI Solutions Architect",
            bio: "Specializes in scalable AI infrastructure and deployment",
            initials: "EJ"
          }
        ]
        
        contactInfo.value = {
          email: "contact@neuralink-ai.com",
          phone: "+1 (555) 123-4567",
          location: "San Francisco, CA"
        }
      }
    }

    onMounted(() => {
      initDarkMode()
      fetchData()
    })

    return {
      isDarkMode,
      toggleDarkMode,
      services,
      team,
      contactInfo,
      showContactModal,
      showQuoteModal,
      isSubmitting,
      isSubmittingQuote,
      contactForm,
      quoteForm,
      calculator,
      openContactModal,
      closeContactModal,
      submitContactForm,
      openQuoteModal,
      closeQuoteModal,
      submitQuoteRequest,
      calculateEstimate,
      getServiceName,
      formatNumber,
      // Quiz
      quizQuestions,
      quizAnswers,
      quizSubmitted,
      quizScore,
      maxQuizScore,
      isQuizComplete,
      quizLevel,
      focusAreas,
      quizSummary,
      calculateQuiz,
      resetQuiz,
      contactFromQuiz
    }
  }
}
</script>
