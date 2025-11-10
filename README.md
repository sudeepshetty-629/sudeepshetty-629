import React, { useEffect, useRef, useState } from 'react';
import { Camera, Code, Brain, Eye, Cloud, Zap, Cpu, Database, GitBranch, Award } from 'lucide-react';

const GitHubProfile3D = () => {
  const canvasRef = useRef(null);
  const [mousePos, setMousePos] = useState({ x: 0, y: 0 });
  const [activeSection, setActiveSection] = useState(0);

  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;

    const ctx = canvas.getContext('2d');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    // Particle system
    class Particle {
      constructor() {
        this.reset();
      }

      reset() {
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
        this.z = Math.random() * 1000;
        this.size = Math.random() * 2 + 1;
        this.speedX = (Math.random() - 0.5) * 0.5;
        this.speedY = (Math.random() - 0.5) * 0.5;
        this.speedZ = Math.random() * 2 + 1;
      }

      update() {
        this.z -= this.speedZ;
        this.x += this.speedX;
        this.y += this.speedY;

        if (this.z <= 0) {
          this.reset();
          this.z = 1000;
        }

        if (this.x < 0 || this.x > canvas.width) this.x = Math.random() * canvas.width;
        if (this.y < 0 || this.y > canvas.height) this.y = Math.random() * canvas.height;
      }

      draw() {
        const scale = 1000 / (1000 + this.z);
        const x = (this.x - canvas.width / 2) * scale + canvas.width / 2;
        const y = (this.y - canvas.height / 2) * scale + canvas.height / 2;
        const size = this.size * scale;
        const opacity = (1000 - this.z) / 1000;

        ctx.fillStyle = `rgba(0, 217, 255, ${opacity * 0.8})`;
        ctx.beginPath();
        ctx.arc(x, y, size, 0, Math.PI * 2);
        ctx.fill();

        // Connection lines
        ctx.strokeStyle = `rgba(0, 217, 255, ${opacity * 0.2})`;
        ctx.lineWidth = 0.5;
        ctx.beginPath();
        ctx.moveTo(x, y);
        ctx.lineTo(canvas.width / 2, canvas.height / 2);
        ctx.stroke();
      }
    }

    // Geometric shapes
    class FloatingShape {
      constructor() {
        this.x = Math.random() * canvas.width;
        this.y = Math.random() * canvas.height;
        this.size = Math.random() * 100 + 50;
        this.rotation = Math.random() * Math.PI * 2;
        this.rotationSpeed = (Math.random() - 0.5) * 0.02;
        this.speedX = (Math.random() - 0.5) * 0.3;
        this.speedY = (Math.random() - 0.5) * 0.3;
        this.type = Math.floor(Math.random() * 3);
      }

      update() {
        this.x += this.speedX;
        this.y += this.speedY;
        this.rotation += this.rotationSpeed;

        if (this.x < -this.size) this.x = canvas.width + this.size;
        if (this.x > canvas.width + this.size) this.x = -this.size;
        if (this.y < -this.size) this.y = canvas.height + this.size;
        if (this.y > canvas.height + this.size) this.y = -this.size;
      }

      draw() {
        ctx.save();
        ctx.translate(this.x, this.y);
        ctx.rotate(this.rotation);
        ctx.strokeStyle = 'rgba(0, 217, 255, 0.15)';
        ctx.lineWidth = 2;

        if (this.type === 0) {
          // Hexagon
          ctx.beginPath();
          for (let i = 0; i < 6; i++) {
            const angle = (Math.PI / 3) * i;
            const x = Math.cos(angle) * this.size;
            const y = Math.sin(angle) * this.size;
            if (i === 0) ctx.moveTo(x, y);
            else ctx.lineTo(x, y);
          }
          ctx.closePath();
          ctx.stroke();
        } else if (this.type === 1) {
          // Triangle
          ctx.beginPath();
          for (let i = 0; i < 3; i++) {
            const angle = (Math.PI * 2 / 3) * i;
            const x = Math.cos(angle) * this.size;
            const y = Math.sin(angle) * this.size;
            if (i === 0) ctx.moveTo(x, y);
            else ctx.lineTo(x, y);
          }
          ctx.closePath();
          ctx.stroke();
        } else {
          // Square
          ctx.strokeRect(-this.size / 2, -this.size / 2, this.size, this.size);
        }

        ctx.restore();
      }
    }

    const particles = Array.from({ length: 150 }, () => new Particle());
    const shapes = Array.from({ length: 8 }, () => new FloatingShape());

    const animate = () => {
      ctx.fillStyle = 'rgba(10, 10, 30, 0.1)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      shapes.forEach(shape => {
        shape.update();
        shape.draw();
      });

      particles.forEach(particle => {
        particle.update();
        particle.draw();
      });

      requestAnimationFrame(animate);
    };

    animate();

    const handleResize = () => {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    };

    window.addEventListener('resize', handleResize);

    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  useEffect(() => {
    const handleMouseMove = (e) => {
      setMousePos({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);

  const projects = [
    {
      title: "Multi-Modal RAG System",
      icon: <Brain className="w-6 h-6" />,
      highlights: ["80% reduction in hallucinations", "40% better retrieval", "10K+ documents"],
      tech: "PyTorch, Gemini, FAISS, FastAPI, React"
    },
    {
      title: "Autonomous Driving",
      icon: <Camera className="w-6 h-6" />,
      highlights: ["Real-time 3D visualization", "LiDAR + Camera fusion", "78.6% accuracy"],
      tech: "PyTorch, YOLOv8, PV-RCNN, Babylon.js"
    },
    {
      title: "Aircraft Signal Chatbot",
      icon: <Zap className="w-6 h-6" />,
      highlights: ["70% monitoring efficiency", "90%+ forecasting accuracy"],
      tech: "LLaMA 3.1, LangChain, Flask"
    },
    {
      title: "Underwater Detection",
      icon: <Eye className="w-6 h-6" />,
      highlights: ["30+ FPS processing", "78% detection accuracy"],
      tech: "YOLOv5, OpenCV, Streamlit"
    }
  ];

  const skills = [
    { name: "Generative AI", icon: <Brain />, level: 95 },
    { name: "Machine Learning", icon: <Cpu />, level: 90 },
    { name: "Computer Vision", icon: <Eye />, level: 90 },
    { name: "MLOps", icon: <Cloud />, level: 85 },
    { name: "Data Science", icon: <Database />, level: 85 }
  ];

  return (
    <div className="relative min-h-screen bg-gradient-to-br from-slate-900 via-purple-900 to-slate-900 text-white overflow-hidden">
      {/* 3D Canvas Background */}
      <canvas 
        ref={canvasRef} 
        className="fixed inset-0 pointer-events-none"
        style={{ opacity: 0.6 }}
      />

      {/* Gradient Overlay */}
      <div className="fixed inset-0 bg-gradient-to-b from-transparent via-purple-900/20 to-slate-900/50 pointer-events-none" />

      {/* Content */}
      <div className="relative z-10 max-w-7xl mx-auto px-4 py-12">
        {/* Hero Section */}
        <div className="text-center mb-20 animate-fade-in">
          <div className="inline-block mb-6 relative">
            <div className="absolute inset-0 bg-cyan-400 blur-3xl opacity-30 animate-pulse" />
            <h1 className="text-7xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-cyan-400 via-blue-500 to-purple-600 relative">
              Sudeep N Shetty
            </h1>
          </div>
          
          <div className="flex items-center justify-center gap-4 mb-6">
            <div className="h-px w-20 bg-gradient-to-r from-transparent to-cyan-400" />
            <p className="text-2xl text-cyan-400 font-light tracking-wider">
              AI & Machine Learning Engineer
            </p>
            <div className="h-px w-20 bg-gradient-to-l from-transparent to-cyan-400" />
          </div>

          <p className="text-lg text-gray-300 max-w-2xl mx-auto mb-8">
            Building intelligent systems with Generative AI, Computer Vision & LLMOps
          </p>

          <div className="flex gap-4 justify-center">
            <a href="mailto:sudeepshetty0629@gmail.com" 
               className="px-6 py-3 bg-gradient-to-r from-cyan-500 to-blue-500 rounded-lg hover:from-cyan-600 hover:to-blue-600 transition-all transform hover:scale-105 shadow-lg shadow-cyan-500/50">
              Get in Touch
            </a>
            <a href="https://github.com/sudeepshetty-629" 
               className="px-6 py-3 bg-white/10 backdrop-blur-sm rounded-lg hover:bg-white/20 transition-all transform hover:scale-105 border border-white/20">
              View GitHub
            </a>
          </div>
        </div>

        {/* Stats Cards */}
        <div className="grid grid-cols-1 md:grid-cols-4 gap-6 mb-20">
          {[
            { icon: <Code />, label: "Projects", value: "15+" },
            { icon: <GitBranch />, label: "Repositories", value: "30+" },
            { icon: <Award />, label: "Certifications", value: "5+" },
            { icon: <Zap />, label: "Tech Stack", value: "25+" }
          ].map((stat, i) => (
            <div key={i} 
                 className="relative group cursor-pointer"
                 style={{ animationDelay: `${i * 0.1}s` }}>
              <div className="absolute inset-0 bg-gradient-to-r from-cyan-500 to-blue-500 rounded-xl blur-xl opacity-0 group-hover:opacity-50 transition-opacity" />
              <div className="relative bg-white/5 backdrop-blur-md border border-white/10 rounded-xl p-6 hover:border-cyan-400/50 transition-all">
                <div className="flex items-center gap-4">
                  <div className="p-3 bg-gradient-to-br from-cyan-400 to-blue-500 rounded-lg">
                    {React.cloneElement(stat.icon, { className: "w-6 h-6" })}
                  </div>
                  <div>
                    <p className="text-3xl font-bold text-cyan-400">{stat.value}</p>
                    <p className="text-sm text-gray-400">{stat.label}</p>
                  </div>
                </div>
              </div>
            </div>
          ))}
        </div>

        {/* Featured Projects */}
        <div className="mb-20">
          <h2 className="text-4xl font-bold mb-12 text-center bg-clip-text text-transparent bg-gradient-to-r from-cyan-400 to-blue-500">
            Featured Projects
          </h2>
          <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
            {projects.map((project, i) => (
              <div key={i} 
                   className="relative group"
                   style={{ animationDelay: `${i * 0.15}s` }}>
                <div className="absolute inset-0 bg-gradient-to-r from-cyan-500 to-blue-500 rounded-2xl blur-xl opacity-0 group-hover:opacity-40 transition-opacity" />
                <div className="relative bg-white/5 backdrop-blur-md border border-white/10 rounded-2xl p-8 hover:border-cyan-400/50 transition-all hover:transform hover:scale-[1.02]">
                  <div className="flex items-center gap-4 mb-4">
                    <div className="p-3 bg-gradient-to-br from-cyan-400 to-blue-500 rounded-lg">
                      {project.icon}
                    </div>
                    <h3 className="text-2xl font-bold">{project.title}</h3>
                  </div>
                  
                  <div className="space-y-2 mb-4">
                    {project.highlights.map((highlight, j) => (
                      <div key={j} className="flex items-center gap-2">
                        <div className="w-1.5 h-1.5 bg-cyan-400 rounded-full" />
                        <p className="text-sm text-gray-300">{highlight}</p>
                      </div>
                    ))}
                  </div>

                  <p className="text-xs text-cyan-400 font-mono">{project.tech}</p>
                </div>
              </div>
            ))}
          </div>
        </div>

        {/* Skills Section */}
        <div className="mb-20">
          <h2 className="text-4xl font-bold mb-12 text-center bg-clip-text text-transparent bg-gradient-to-r from-cyan-400 to-blue-500">
            Core Competencies
          </h2>
          <div className="space-y-6">
            {skills.map((skill, i) => (
              <div key={i} className="relative group">
                <div className="flex items-center justify-between mb-2">
                  <div className="flex items-center gap-3">
                    <div className="p-2 bg-gradient-to-br from-cyan-400 to-blue-500 rounded-lg">
                      {React.cloneElement(skill.icon, { className: "w-5 h-5" })}
                    </div>
                    <span className="font-semibold">{skill.name}</span>
                  </div>
                  <span className="text-cyan-400 font-bold">{skill.level}%</span>
                </div>
                <div className="h-3 bg-white/5 rounded-full overflow-hidden backdrop-blur-sm border border-white/10">
                  <div 
                    className="h-full bg-gradient-to-r from-cyan-400 to-blue-500 rounded-full transition-all duration-1000 ease-out"
                    style={{ 
                      width: `${skill.level}%`,
                      animationDelay: `${i * 0.1}s`
                    }}
                  />
                </div>
              </div>
            ))}
          </div>
        </div>

        {/* Contact Footer */}
        <div className="text-center pt-12 border-t border-white/10">
          <p className="text-gray-400 mb-4">
            💬 Open for opportunities in AI/ML Engineering & Generative AI
          </p>
          <p className="text-sm text-gray-500">
            ⚡ I turn coffee ☕ into code and data into intelligence 🧠
          </p>
        </div>
      </div>

      {/* Floating Elements */}
      <div 
        className="fixed w-96 h-96 bg-cyan-500/10 rounded-full blur-3xl pointer-events-none transition-all duration-300"
        style={{
          left: mousePos.x - 192,
          top: mousePos.y - 192,
        }}
      />
    </div>
  );
};

export default GitHubProfile3D;
