# 标签

> 按标签浏览文章

<div id="tags-container">
  <p>标签加载中...</p>
</div>

<style>
  /* 深色模式支持 */
  .dark #tags-container a {
    color: #e0e0e0 !important;
  }
  
  .dark #tags-container a:hover {
    color: #bbdefb !important;
  }
  
  .dark #tags-container h2 {
    color: #e0e0e0;
    border-bottom-color: #444;
  }
  
  .dark #tags-container ul li a {
    color: #90caf9;
  }
  
  .dark #tags-container ul li a:hover {
    color: #bbdefb;
  }
  
  .dark #tags-container div a {
    background-color: #444;
    color: #e0e0e0;
  }
  
  .dark #tags-container div a:hover {
    background-color: #555;
  }
</style>

<script>
  // 标签数据
  const tagsData = {
    "LLM": [
      {"title": "在语义的弯曲空间中导航：大语言模型、流形与Prompt工程", "url": "essays/LLM/LLM&Manifolds.md"}
    ],
    "MATSIM": [
      {"title": "Matsim是如何仿真的", "url": "essays/matsim/howMATSimWorks.md"},
      {"title": "仿真效率魔咒：不可能三角（观PSIM有感）", "url": "essays/matsim/psim.md"},
      {"title": "关于协同进化 Co-evolution", "url": "essays/matsim/co-evolution.md"},
      {"title": "MATSim中的路径求解", "url": "essays/matsim/Router.md"},
      {"title": "如何平衡规模与效率", "url": "essays/matsim/scaling.md"},
      {"title": "公交建模", "url": "essays/matsim/PublicTransit.md"}
    ],
    "SUMO": [
      {"title": "Traci环境配置", "url": "essays/SUMO/1_Traci_env_init.md"},
      {"title": "SUMO框架和核心模块功能", "url": "essays/SUMO/2_Frame_and_Core.md"},
      {"title": "车辆行为模型", "url": "essays/SUMO/5_Car_following_and_lane_change_model.md"},
      {"title": "PLEXE车辆编队", "url": "essays/SUMO/4_Platooning.md"},
      {"title": "使用Traci订阅实时仿真数据", "url": "essays/SUMO/3_Data_subscription.md"}
    ],
    "PTALs": [
      {"title": "PTALs", "url": "essays/ptals/intro_ptal.md"}
    ],
    "GAME AI": [
      {"title": "从RTS游戏到微观交通仿真", "url": "essays/wander/从RTS游戏到微观交通仿真.md"},
      {"title": "GDC AI Navigation访谈摘录", "url": "essays/wander/GDC_AI_Navigation文字稿.md"}
    ],
    "随笔": [
      {"title": "微观动机与宏观行为", "url": "essays/wander/微观动机与宏观行为.md"}
    ],
    "文摘": [
      {"title": "布雷顿森林体系的前世、今生和未来", "url": "essays/wander/时运变迁中文序.md"},
      {"title": "高频交易背后的数学思想", "url": "essays/wander/高频交易背后的数学思想.md"}
    ],
    "交通仿真": [
      {"title": "Matsim是如何仿真的", "url": "essays/matsim/howMATSimWorks.md"},
      {"title": "仿真效率魔咒：不可能三角（观PSIM有感）", "url": "essays/matsim/psim.md"},
      {"title": "关于协同进化 Co-evolution", "url": "essays/matsim/co-evolution.md"},
      {"title": "MATSim中的路径求解", "url": "essays/matsim/Router.md"},
      {"title": "如何平衡规模与效率", "url": "essays/matsim/scaling.md"},
      {"title": "公交建模", "url": "essays/matsim/PublicTransit.md"},
      {"title": "Traci环境配置", "url": "essays/SUMO/1_Traci_env_init.md"},
      {"title": "SUMO框架和核心模块功能", "url": "essays/SUMO/2_Frame_and_Core.md"},
      {"title": "车辆行为模型", "url": "essays/SUMO/5_Car_following_and_lane_change_model.md"},
      {"title": "PLEXE车辆编队", "url": "essays/SUMO/4_Platooning.md"},
      {"title": "使用Traci订阅实时仿真数据", "url": "essays/SUMO/3_Data_subscription.md"},
      {"title": "从RTS游戏到微观交通仿真", "url": "essays/wander/从RTS游戏到微观交通仿真.md"}
    ],
    "人工智能": [
      {"title": "在语义的弯曲空间中导航：大语言模型、流形与Prompt工程", "url": "essays/LLM/LLM&Manifolds.md"},
      {"title": "GDC AI Navigation访谈摘录", "url": "essays/wander/GDC_AI_Navigation文字稿.md"}
    ]
  };

  // 渲染标签云
  function renderTags() {
    const container = document.getElementById('tags-container');
    container.innerHTML = '';

    // 创建标签云
    const tagCloud = document.createElement('div');
    tagCloud.style.marginBottom = '2rem';
    tagCloud.style.display = 'flex';
    tagCloud.style.flexWrap = 'wrap';
    tagCloud.style.gap = '0.5rem';

    Object.keys(tagsData).forEach(tag => {
      const tagLink = document.createElement('a');
      tagLink.href = `#${tag}`;
      tagLink.style.display = 'inline-block';
      tagLink.style.padding = '0.3rem 0.8rem';
      tagLink.style.backgroundColor = '#f0f0f0';
      tagLink.style.borderRadius = '1rem';
      tagLink.style.textDecoration = 'none';
      tagLink.style.color = '#333';
      tagLink.style.fontSize = '0.9rem';
      tagLink.style.transition = 'all 0.2s ease';

      tagLink.addEventListener('mouseover', function() {
        this.style.backgroundColor = '#e0e0e0';
      });

      tagLink.addEventListener('mouseout', function() {
        this.style.backgroundColor = '#f0f0f0';
      });

      tagLink.textContent = `${tag} (${tagsData[tag].length})`;
      tagCloud.appendChild(tagLink);
    });

    container.appendChild(tagCloud);

    // 创建标签分类
    Object.keys(tagsData).forEach(tag => {
      const tagSection = document.createElement('section');
      tagSection.id = tag;
      tagSection.style.marginBottom = '2rem';

      const tagTitle = document.createElement('h2');
      tagTitle.textContent = `${tag} (${tagsData[tag].length})`;
      tagTitle.style.borderBottom = '1px solid #eee';
      tagTitle.style.paddingBottom = '0.5rem';
      tagSection.appendChild(tagTitle);

      const articleList = document.createElement('ul');
      articleList.style.listStyle = 'none';
      articleList.style.padding = '0';

      tagsData[tag].forEach(article => {
        const listItem = document.createElement('li');
        listItem.style.margin = '0.5rem 0';
        
        const articleLink = document.createElement('a');
        articleLink.href = article.url;
        articleLink.textContent = article.title;
        articleLink.style.textDecoration = 'none';
        articleLink.style.color = '#3F51B5';
        articleLink.style.transition = 'color 0.2s ease';

        articleLink.addEventListener('mouseover', function() {
          this.style.color = '#1E88E5';
          this.style.textDecoration = 'underline';
        });

        articleLink.addEventListener('mouseout', function() {
          this.style.color = '#3F51B5';
          this.style.textDecoration = 'none';
        });

        listItem.appendChild(articleLink);
        articleList.appendChild(listItem);
      });

      tagSection.appendChild(articleList);
      container.appendChild(tagSection);
    });
  }

  // 页面加载完成后渲染标签
  window.addEventListener('DOMContentLoaded', renderTags);
</script>
