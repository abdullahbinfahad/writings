---
layout: default
title: Archives
permalink: /archive.html
---
<div class="archive-layout fade-in">
    
    <!-- LEFT COLUMN: Timeline Accordion -->
    <div class="archive-timeline">
        <h2 class="archive-section-title"><i class="fas fa-clock"></i> By Timeline</h2>
        {% assign postsByYear = site.posts | group_by_exp:"post", "post.date | date: '%Y'" %}
        {% for year in postsByYear %}
            <h3 id="{{ year.name }}" class="archive-year-head">{{ year.name }}</h3>
            {% assign postsByMonth = year.items | group_by_exp:"post", "post.date | date: '%B'" %}
            {% for month in postsByMonth %}
            <div class="month-item">
                <div class="month-header" data-date="{{ year.name }}-{{ month.name | date: '%m' }}">
                    <span class="month-name">{{ month.name }} <span style="font-weight:400; color:#6b7280; font-size:0.8rem;">({{ month.items.size }} posts)</span></span>
                    <span class="arrow"><i class="fas fa-chevron-right"></i></span>
                </div>
                <div class="month-content">
                    <ul class="archive-list">
                        {% for post in month.items %}
                        <!-- FIXED PART INDEX: Calculate based on the sorted series list -->
                        {% if post.series %}
                            {% assign series_posts = site.posts | where: "series", post.series | sort: "date" %}
                            {% for sp in series_posts %}
                                {% if sp.url == post.url %}
                                    {% assign part_index = forloop.index0 %}
                                    {% break %}
                                {% endif %}
                            {% endfor %}
                        {% else %}
                            {% assign part_index = 0 %}
                        {% endif %}
                        <li>
                            <a href="{{ post.url }}">{{ post.title }}{% if post.series %} (Part {{ part_index | plus: 1 }}){% endif %}</a>
                            <span class="archive-date">{{ post.date | date: "%b %d" }}</span>
                        </li>
                        {% endfor %}
                    </ul>
                </div>
            </div>
            {% endfor %}
        {% endfor %}
    </div>

    <!-- RIGHT COLUMN: Stories, Novels, Poetry & Categories -->
    <div class="archive-categories" style="display: flex; flex-wrap: wrap; gap: 2rem; padding: 0; background: transparent; border: none; border-radius: 0;">
        
        <!-- 1. STORIES & NOVELS HUB (UPDATED WITH ANCHOR ID) -->
        <div class="archive-card story-hub" style="flex: 1 1 280px; min-width: 240px;">
            <h2 class="archive-section-title" style="margin-bottom: 1rem;"><i class="fas fa-book-open"></i> Stories & Novels</h2>
            {% assign series_map = site.posts | group_by_exp:"post", "post.series" %}
            {% assign has_series = false %}
            {% for series in series_map %}
                {% if series.name != "" and series.name != nil %}
                    {% assign has_series = true %}
                    <!-- Added ID here to create a clickable anchor target -->
                    <div class="story-series" id="{{ series.name | slugify }}">
                        <div class="series-header">
                            <span class="series-title">{{ series.name }}</span>
                            <span class="series-count">({{ series.items.size }} parts)</span>
                        </div>
                        <ul class="series-parts">
                            <!-- SORTED BY DATE: Ensures Part 1 is always at the top -->
                            {% assign sorted_parts = series.items | sort: "date" %}
                            {% for post in sorted_parts %}
                            <li>
                                <a href="{{ post.url }}">
                                    {% if post.chapter_name %}
                                        {{ post.chapter_name }}
                                    {% else %}
                                        Part {{ forloop.index }}: {{ post.title }}
                                    {% endif %}
                                </a>
                            </li>
                            {% endfor %}
                        </ul>
                    </div>
                {% endif %}
            {% endfor %}
            {% if has_series == false %}
            <p style="color: #6b7280; font-size: 0.9rem; font-family: 'Inter', sans-serif;">No novels or series published yet.</p>
            {% endif %}
        </div>

        <!-- 2. POETRY HUB -->
        <div class="archive-card" style="flex: 1 1 280px; min-width: 240px;">
            <h2 class="archive-section-title" style="margin-bottom: 1rem;"><i class="fas fa-feather"></i> Poetry</h2>
            {% assign poems = site.categories.Poetry %}
            {% if poems and poems.size > 0 %}
                {% assign sorted_poems = poems | sort: 'date' | reverse %}
                <ul class="archive-list" style="list-style: none; padding: 0; margin: 0;">
                {% for poem in sorted_poems %}
                    <li style="padding: 0.4rem 0; border-bottom: 1px dashed #e5e7eb; display: flex; justify-content: space-between;">
                        <a href="{{ poem.url }}">{{ poem.title }}</a>
                        <span class="archive-date" style="font-size:0.8rem; color:#6b7280;">{{ poem.date | date: "%b %d, %Y" }}</span>
                    </li>
                {% endfor %}
                </ul>
            {% else %}
            <p style="color: #6b7280; font-size: 0.9rem; font-family: 'Inter', sans-serif;">No poems published yet.</p>
            {% endif %}
        </div>

        <!-- 3. CATEGORY FOLDERS (UPDATED LINK TO ANCHOR) -->
        <div class="archive-card" style="flex: 1 1 280px; min-width: 240px;">
            <h2 class="archive-section-title" style="margin-bottom: 1rem;"><i class="fas fa-folder-open"></i> By Category</h2>
            {% if site.categories %}
                {% assign categories = site.categories | sort %}
                {% for category in categories %}
                    <div class="category-folder" id="{{ category[0] | slugify }}">
                        <div class="folder-header">
                            <span class="folder-icon">📁</span>
                            <span class="folder-name">{{ category[0] }}</span>
                            <span class="folder-count">({{ category[1].size }})</span>
                        </div>
                        <ul class="folder-contents">
                            {% assign displayed_series = "" %}
                            {% for post in category[1] %}
                                {% if post.series %}
                                    {% unless displayed_series contains post.series %}
                                        {% assign displayed_series = displayed_series | append: "|" | append: post.series %}
                                        <!-- Changed URL to point to the anchor ID -->
                                        <li><a href="#{{ post.series | slugify }}">{{ post.series }}</a></li>
                                    {% endunless %}
                                {% else %}
                                    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
                                {% endif %}
                            {% endfor %}
                        </ul>
                    </div>
                {% endfor %}
            {% else %}
                <p style="color: #6b7280; font-size: 0.9rem; font-family: 'Inter', sans-serif;">No categories found.</p>
            {% endif %}
        </div>

    </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
    const headers = document.querySelectorAll('.month-header');
    const now = new Date();
    const currentYear = now.getFullYear();
    const currentMonth = now.getMonth() + 1; 
    let targetMonths = [];
    for(let i = 0; i < 3; i++) {
        let d = new Date(currentYear, currentMonth - 1 - i, 1);
        let y = d.getFullYear();
        let m = String(d.getMonth() + 1).padStart(2, '0');
        targetMonths.push(`${y}-${m}`);
    }
    headers.forEach(header => {
        const dateStr = header.dataset.date;
        const content = header.nextElementSibling;
        if(targetMonths.includes(dateStr)) {
            header.classList.add('open');
            content.style.maxHeight = content.scrollHeight + 'px';
        }
        header.addEventListener('click', function() {
            this.classList.toggle('open');
            if(this.classList.contains('open')) {
                content.style.maxHeight = content.scrollHeight + 'px';
            } else {
                content.style.maxHeight = null;
            }
        });
    });

    // Auto-scroll to the anchor if the URL has a hash
    if(window.location.hash) {
        const target = document.querySelector(window.location.hash);
        if(target) {
            setTimeout(() => {
                target.scrollIntoView({ behavior: 'smooth', block: 'start' });
            }, 500); // 500ms delay to wait for the accordion to render
        }
    }
});
</script>
