<script setup>
import { onMounted, onUnmounted, ref } from 'vue'
import * as THREE from 'three'
import Lenis from 'lenis'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'

gsap.registerPlugin(ScrollTrigger)

const projects = [
  {
    number: '01', name: 'JC Decorar', kind: 'API · BACKEND', summary: 'Uma base segura para administrar projetos de decoração.',
    detail: 'API REST desenvolvida com Java 21 e Spring Boot. Inclui operações de cadastro, consulta, atualização e remoção, com autenticação e persistência PostgreSQL.',
    stack: ['Java 21', 'Spring Boot', 'Spring Security', 'JWT', 'PostgreSQL', 'Docker'],
    image: '/images/projects/jc-decorar/foto-1.png', secondary: '/images/projects/jc-decorar/foto-2.png', imageAlt: 'Tela do projeto JC Decorar',
    href: 'https://jc-decorar-site.vercel.app/', label: 'ABRIR PROJETO'
  },
  {
    number: '02', name: 'Macedo Farias', kind: 'PLATAFORMA · CATÁLOGO', summary: 'Uma vitrine digital feita para aproximar produtos e pessoas.',
    detail: 'Catálogo de confeitaria com busca, categorias e área administrativa. Frontend Vue.js conectado a uma API Spring Boot, PostgreSQL e Cloudinary para imagens.',
    stack: ['Vue.js', 'Java', 'Spring Boot', 'PostgreSQL', 'Cloudinary', 'GSAP'],
    image: '/images/projects/macedo-farias/foto-1.png', secondary: '/images/projects/macedo-farias/foto-2.png', imageAlt: 'Tela do catálogo Macedo Farias',
    href: 'https://macedofarias.vercel.app/#/', label: 'ABRIR PROJETO'
  }
]

const canvas = ref(null)
const activeProject = ref('01')
const galleryIndex = ref({ '01': 0, '02': 0 })
let renderer, scene, camera, core, frameId, lenis, ticker
let pointer = { x: 0, y: 0 }
let target = { x: 0, y: 0 }
let cleanupScroll
let dragStartX = null

function onPointer(event) {
  pointer.x = (event.clientX / window.innerWidth - 0.5) * 2
  pointer.y = (event.clientY / window.innerHeight - 0.5) * 2
}

function changeGallery(project, direction) {
  galleryIndex.value[project] = (galleryIndex.value[project] + direction + 2) % 2
}

function startGalleryDrag(event) {
  dragStartX = event.clientX
  event.currentTarget.setPointerCapture?.(event.pointerId)
}

function endGalleryDrag(event, project) {
  if (dragStartX === null) return
  const distance = event.clientX - dragStartX
  if (Math.abs(distance) > 42) changeGallery(project, distance < 0 ? 1 : -1)
  dragStartX = null
}

onMounted(() => {
  document.documentElement.classList.add('ready')
  const reducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches
  try {
    scene = new THREE.Scene()
    camera = new THREE.PerspectiveCamera(34, window.innerWidth / window.innerHeight, 0.1, 100)
    camera.position.set(0, 0, 8)
    renderer = new THREE.WebGLRenderer({ canvas: canvas.value, alpha: true, antialias: true, powerPreference: 'low-power' })
    renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5))
    renderer.setSize(window.innerWidth, window.innerHeight)
    renderer.outputColorSpace = THREE.SRGBColorSpace
    scene.add(new THREE.AmbientLight(0xc9d0c6, 1.3))
    const key = new THREE.PointLight(0xd4ee88, 38, 20)
    key.position.set(3, 2, 4)
    scene.add(key)
    const fill = new THREE.PointLight(0xe2e7db, 24, 20)
    fill.position.set(-4, -2, 2)
    scene.add(fill)

    core = new THREE.Group()
    const wire = new THREE.Mesh(new THREE.IcosahedronGeometry(1.5, 2), new THREE.MeshStandardMaterial({ color: 0xaab19d, roughness: 0.35, metalness: 0.82, wireframe: true, transparent: true, opacity: 0.45 }))
    const shell = new THREE.Mesh(new THREE.IcosahedronGeometry(1.1, 1), new THREE.MeshStandardMaterial({ color: 0x717a65, roughness: 0.24, metalness: 0.85, flatShading: true }))
    const orbit = new THREE.Mesh(new THREE.TorusGeometry(1.92, 0.008, 8, 180), new THREE.MeshBasicMaterial({ color: 0xc9e68b, transparent: true, opacity: 0.72 }))
    orbit.rotation.set(1.12, 0.3, -0.2)
    const orbit2 = orbit.clone()
    orbit2.scale.setScalar(1.16)
    orbit2.rotation.set(0.25, 1.25, 0.6)
    core.add(wire, shell, orbit, orbit2)
    scene.add(core)
    const stars = new THREE.BufferGeometry()
    const points = Array.from({ length: 360 }, () => (Math.random() - 0.5) * 18)
    stars.setAttribute('position', new THREE.Float32BufferAttribute(points, 3))
    scene.add(new THREE.Points(stars, new THREE.PointsMaterial({ color: 0xbcc2b4, size: 0.012, transparent: true, opacity: 0.42 })))

    const render = () => {
      target.x += (pointer.y * 0.18 - target.x) * 0.035
      target.y += (pointer.x * 0.22 - target.y) * 0.035
      core.rotation.x = target.x
      core.rotation.y += 0.0018 + target.y * 0.006
      core.position.y = Math.sin(performance.now() * 0.00045) * 0.08
      renderer.render(scene, camera)
      frameId = requestAnimationFrame(render)
    }
    render()
    window.addEventListener('pointermove', onPointer, { passive: true })
    window.addEventListener('resize', resize)
  } catch {
    canvas.value?.classList.add('webgl-fallback')
  }

  if (!reducedMotion) {
    lenis = new Lenis({ duration: 1.15, smoothWheel: true, wheelMultiplier: 0.85 })
    ticker = (time) => lenis.raf(time * 1000)
    gsap.ticker.add(ticker)
    gsap.ticker.lagSmoothing(0)
    cleanupScroll = lenis.on('scroll', ScrollTrigger.update)
  }
  gsap.from('.hero-copy > *', { y: 24, opacity: 0, duration: 0.85, stagger: 0.12, ease: 'power3.out', delay: 0.15 })
  gsap.utils.toArray('[data-reveal]').forEach((element) => gsap.from(element, {
    y: 28, opacity: 0, duration: 0.8, ease: 'power2.out',
    scrollTrigger: { trigger: element, start: 'top 86%', once: true }
  }))
  projects.forEach((project) => ScrollTrigger.create({ trigger: `#project-${project.number}`, start: 'top 55%', end: 'bottom 55%', onEnter: () => { activeProject.value = project.number }, onEnterBack: () => { activeProject.value = project.number } }))
})

function resize() {
  if (!renderer || !camera) return
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5))
  renderer.setSize(window.innerWidth, window.innerHeight)
}

onUnmounted(() => {
  window.removeEventListener('pointermove', onPointer)
  window.removeEventListener('resize', resize)
  cancelAnimationFrame(frameId)
  cleanupScroll?.()
  lenis?.destroy()
  if (ticker) gsap.ticker.remove(ticker)
  ScrollTrigger.getAll().forEach((trigger) => trigger.kill())
  scene?.traverse((object) => {
    object.geometry?.dispose()
    if (Array.isArray(object.material)) object.material.forEach((material) => material.dispose())
    else object.material?.dispose()
  })
  renderer?.dispose()
})
</script>

<template>
  <div class="portfolio-shell">
    <canvas ref="canvas" class="scene-canvas" aria-hidden="true"></canvas>
    <header class="topbar">
      <a class="wordmark" href="#inicio" aria-label="André Felipe, início"><span class="wordmark-mark">AF</span><span>ANDRÉ FELIPE <small>PORTFÓLIO / 01</small></span></a>
      <div class="top-status"><span class="status-dot"></span> SISTEMA ONLINE <span class="status-divider">/</span> BRASIL</div>
      <a class="top-contact" href="#projetos">PROJETOS <span>↗</span></a>
    </header>

    <main>
      <section id="inicio" class="hero section-pad">
        <div class="hero-meta mono"><span>01 — SISTEMA</span></div>
        <div class="hero-copy">
          <p class="eyebrow mono"><i></i> DESENVOLVIMENTO DE SOFTWARE · BR</p>
          <h1>André<br /><span>Felipe</span><sup>®</sup></h1>
          <div class="hero-bottom"><p>Construo sistemas, APIs<br />e experiências para a web.</p><a class="round-link" href="#projetos" aria-label="Explorar projetos">↓</a></div>
        </div>
        <div class="hero-index mono"><span>JAVA / SPRING</span><span>VUE / WEB</span></div>
        <a class="scroll-cue mono" href="#perfil"><span></span>ROLE PARA EXPLORAR</a>
      </section>

      <section id="perfil" class="profile section-pad">
        <div class="section-kicker mono" data-reveal><span>02 / PERFIL</span><span>ARQUIVO PESSOAL</span></div>
        <div class="profile-layout">
          <h2 data-reveal>Curiosidade<br />que vira <em>sistema.</em></h2>
          <div class="profile-copy" data-reveal><p>Estudante de Análise e Desenvolvimento de Sistemas, com foco em backend e aplicações web.</p><p>Gosto de conectar APIs, dados e interfaces em produtos úteis — do primeiro endpoint à experiência que chega às pessoas.</p><div class="profile-status mono"><span>FOCO</span><b>BACKEND / WEB</b><span>ESTADO</span><b>SEMPRE CONSTRUINDO</b></div></div>
        </div>
        <div class="profile-foot mono" data-reveal><span>JAVA · SPRING BOOT · VUE.JS</span><span>BR — 2026</span></div>
      </section>

      <section id="projetos" class="projects section-pad">
        <div class="section-kicker mono" data-reveal><span>03 / PROJETOS</span></div>
        <div class="projects-heading" data-reveal><h2>Trabalho em<br /><em>execução.</em></h2><p>Dois projetos. Problemas diferentes.<br />Uma vontade de fazer funcionar.</p></div>
        <article v-for="project in projects" :id="`project-${project.number}`" :key="project.number" class="project-entry">
          <div class="project-grid">
            <div class="project-info"><h3>{{ project.name }}<sup>↗</sup></h3><p class="project-summary">{{ project.summary }}</p><p class="project-detail">{{ project.detail }}</p><div class="tech-list"><span v-for="tech in project.stack" :key="tech">{{ tech }}</span></div><a class="project-link mono" :href="project.href" target="_blank" rel="noreferrer">{{ project.label }} <span>↗</span></a></div>
            <div :class="['project-visual', { 'project-visual-macedo': project.number === '02', 'project-visual-jc': project.number === '01' }]" role="group" :aria-label="`Galeria de imagens: ${project.name}`" @pointerdown="startGalleryDrag" @pointerup="endGalleryDrag($event, project.number)" @pointercancel="dragStartX = null">
              <img :src="galleryIndex[project.number] === 0 ? project.image : project.secondary" :alt="galleryIndex[project.number] === 0 ? project.imageAlt : `Segunda captura do ${project.name}`" loading="lazy" draggable="false" />
              <span class="visual-stamp mono">{{ String(galleryIndex[project.number] + 1).padStart(2, '0') }} / 02</span>
              <div class="gallery-controls" :aria-label="`Controles da galeria ${project.name}`">
                <button type="button" :aria-label="`Imagem anterior de ${project.name}`" @pointerdown.stop @click="changeGallery(project.number, -1)">←</button>
                <button type="button" :aria-label="`Próxima imagem de ${project.name}`" @pointerdown.stop @click="changeGallery(project.number, 1)">→</button>
              </div>
              <span class="gallery-hint mono">ARRASTE OU USE AS SETAS</span>
            </div>
          </div>
        </article>
      </section>

      <section id="contato" class="contact section-pad">
        <div class="section-kicker mono" data-reveal><span>04 / PRÓXIMO PASSO</span><span>CANAL ABERTO</span></div>
        <div class="contact-body" data-reveal><p class="eyebrow mono"><i></i> PRÓXIMA CONVERSA</p><h2>Uma boa ideia<br />começa com <em>oi.</em></h2><p class="contact-note">Quer conversar sobre um projeto ou oportunidade? Me chama por aqui.</p><nav class="contact-links" aria-label="Canais de contato"><a href="https://www.linkedin.com/in/andre-felipe-339039339/" target="_blank" rel="noreferrer">LINKEDIN <span>↗</span></a><a href="https://www.instagram.com/andre.felipe99/" target="_blank" rel="noreferrer">INSTAGRAM <span>↗</span></a><a href="https://wa.me/5521990757721" target="_blank" rel="noreferrer">WHATSAPP <small>+55 21 99075-7721</small><span>↗</span></a></nav></div>
      </section>
    </main>

    <footer class="footer section-pad"><a class="wordmark" href="#inicio"><span class="wordmark-mark">AF</span><span>ANDRÉ FELIPE <small>SOFTWARE DEVELOPER</small></span></a><span class="mono footer-end">FIM DO SISTEMA · © 2026</span><a class="back-top mono" href="#inicio">VOLTAR AO TOPO ↑</a></footer>
  </div>
</template>
