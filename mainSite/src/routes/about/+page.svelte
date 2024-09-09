<script lang="ts">
  import { onMount } from 'svelte';
  import aboutData from '$lib/about.json';
  import skillsData from '$lib/skills.json';

  let expandedBox: string | null = null;
  let cursorTrail: HTMLDivElement[] = [];
  let container: HTMLDivElement;

  function toggleExpand(box: string) {
    expandedBox = expandedBox === box ? null : box;
  }

  function handleMouseMove(event: MouseEvent) {
    const trail = document.createElement('div');
    trail.className = 'cursor-trail';
    trail.style.left = `${event.clientX}px`;
    trail.style.top = `${event.clientY}px`;
    container.appendChild(trail);
    cursorTrail.push(trail);

    if (cursorTrail.length > 20) {
      const oldestTrail = cursorTrail.shift();
      oldestTrail?.remove();
    }

    setTimeout(() => {
      trail.remove();
      cursorTrail = cursorTrail.filter(t => t !== trail);
    }, 500);
  }

  onMount(() => {
    container.addEventListener('mousemove', handleMouseMove);
    return () => {
      container.removeEventListener('mousemove', handleMouseMove);
    };
  });
</script>

<div class="container" bind:this={container}>
  <img src="{aboutData.profilePicture}" alt="Profile Picture" class="profile-picture" />
  <h1 class="name">ServerBP</h1>
  
  <div class="boxes-container">
    {#each ['Me, Personally', 'Me, Online', 'My Projects'] as box}
      <div class="box" class:expanded={expandedBox === box} on:click={() => toggleExpand(box)}>
        <h2>{box}</h2>
        <hr />
        {#if box === 'My Projects'}
          <ul>
            {#each aboutData.projects as project}
              <li>
                <img src={project.image} alt={project.name} style="max-width: 35px;"/>
                <a href={project.link} target="_blank" rel="noopener noreferrer">{project.name}</a>
              </li>
            {/each}
          </ul>
        {:else}
          <p class="content">{aboutData[box.toLowerCase().replace(', ', '')]}</p>
          {#if !expandedBox}
            <button class="read-more">
              Read More
              <span class="material-icons">arrow_downward</span>
            </button>
          {/if}
        {/if}
      </div>
    {/each}
  </div>

  <h3 class="utilities-heading">Utilities:</h3>
  <div class="skills-container">
    {#each skillsData as skill}
      <div class="skill-box" style="--skill-color: {skill.color}">
        <img src={skill.icon} alt={skill.name} />
        <p>{skill.name}</p>
      </div>
    {/each}
  </div>

  <div class="social-links">
    {#each ['github', 'youtube', 'twitch', 'discord', 'twitter', 'email', 'beatleader', 'scoresaber'] as platform}
      <a href={aboutData.socialLinks[platform]} target="_blank" rel="noopener noreferrer">
        <img src="/icons/{platform}.svg" alt={platform} />
      </a>
    {/each}
  </div>
</div>

<style>
  :global(body) {
    background-color: #121212;
    color: #ffffff;
    font-family: Arial, sans-serif;
  }

  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
    position: relative;
    overflow: hidden;
  }

  .cursor-trail {
    position: absolute;
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background-color: rgba(200, 200, 200, 0.6);
    pointer-events: none;
    z-index: 9999;
    animation: fadeOut 0.5s ease-out forwards;
  }

  @keyframes fadeOut {
    to {
      opacity: 0;
      transform: scale(2);
    }
  }

  .profile-picture {
    display: block;
    width: 200px;
    height: 200px;
    object-fit: cover;
    border-radius: 50%;
    margin: 0 auto 2rem;
  }

  .boxes-container {
    display: flex;
    gap: 1rem;
    margin-bottom: 2rem;
    transition: all 0.5s ease;
  }

  .box {
    flex: 1;
    background-color: #1e1e1e;
    border: 1px solid #4fc3f7;
    border-radius: 8px;
    padding: 1rem;
    transition: all 0.5s ease;
    cursor: pointer;
  }

  .box:hover {
    box-shadow: 0 0 15px rgba(79, 195, 247, 0.5);
  }

  .box.expanded {
    flex-basis: 100%;
    order: -1;
  }

  .box h2 {
    text-align: center;
    font-weight: bold;
    margin-bottom: 0.5rem;
  }

  .box hr {
    border: none;
    border-top: 1px solid #4fc3f7;
    margin-bottom: 1rem;
  }

  .box .content {
    max-height: 100px;
    overflow: hidden;
    position: relative;
  }

  .box .content::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 50px;
    background: linear-gradient(transparent, #1e1e1e);
  }

  .box.expanded .content {
    max-height: none;
  }

  .box.expanded .content::after {
    display: none;
  }

  .read-more {
    display: flex;
    align-items: center;
    justify-content: center;
    background: none;
    border: none;
    color: #4fc3f7;
    cursor: pointer;
    margin-top: 1rem;
  }

  .skills-container {
    display: flex;
    flex-wrap: wrap;
    gap: 1rem;
    justify-content: center;
  }

  .skill-box {
    width: calc(25% - 1rem);
    aspect-ratio: 1;
    background-color: #1e1e1e;
    border: 1px solid var(--skill-color, #4fc3f7);
    border-radius: 8px;
    padding: 1rem;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    transition: all 0.5s ease;
  }

  .skill-box:hover {
    box-shadow: 0 0 15px var(--skill-color, rgba(79, 195, 247, 0.5));
  }

  .skill-box img {
    max-width: 60%;
    max-height: 60%;
    margin-bottom: 0.5rem;
  }

  .social-links {
    display: flex;
    justify-content: center;
    gap: 1rem;
    margin-top: 2rem;
  }

  .social-links a {
    display: inline-block;
    width: 40px;
    height: 40px;
    transition: transform 0.3s ease;
  }

  .social-links a:hover {
    transform: scale(1.1);
  }

  .social-links img {
    width: 100%;
    height: 100%;
  }

  .name {
    text-align: center;
    margin-bottom: 2rem;
  }

  .utilities-heading {
    text-align: center;
    font-size: 1.5em;
    margin-bottom: 1rem;
    text-decoration: underline;
  }

  @media (max-width: 768px) {
    .boxes-container {
      flex-direction: column;
    }

    .box {
      flex-basis: 100%;
    }

    .skill-box {
      width: calc(50% - 1rem);
    }
  }
</style>