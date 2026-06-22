# 兴奋-抑制神经网络 Hopf 分叉数值模拟

## 作业说明

本作业对**兴奋-抑制（E-I）神经网络**的 Hopf 分叉现象进行理论推导与数值验证，揭示系统在抑制性时间常数 $\tau_I$ 变化时从稳定平衡点过渡到振荡状态的动力学行为。

---

## 环境要求

- **开发环境**：Python 3.14.4
- **运行环境**：Jupyter Notebook
- **依赖库**：numpy, matplotlib, scipy, os, platform
- （版本见 requirements.txt）

安装第三方依赖：

```bash
pip install -r requirements.txt
```

---
## 运行方法

使用 Jupyter Notebook**

```bash
jupyter notebook 第三题.ipynb
```

打开后，点击菜单栏 **Cell → Run All** 运行全部代码，图像将自动保存至 ./hopf_figs/

---

## 数学模型

系统由以下两个耦合常微分方程描述：

$$\tau_E \frac{dv_E}{dt} = -v_E + [M_{EE} v_E - M_{EI} v_I + h_E]_+$$

$$\tau_I \frac{dv_I}{dt} = -v_I + [M_{IE} v_E - M_{II} v_I + h_I]_+$$

其中 $[\cdot]_+ = \max(\cdot,\ 0)$ 为 ReLU 激活函数，$v_E$、$v_I$ 分别为兴奋性与抑制性神经元群的平均放电率。

---

## 默认参数

| 参数 | 符号 | 取值 |
|------|------|------|
| 兴奋→兴奋权重 | $M_{EE}$ | 1.5 |
| 兴奋→抑制权重 | $M_{EI}$ | 1.0 |
| 抑制→兴奋权重 | $M_{IE}$ | 1.2 |
| 抑制→抑制权重 | $M_{II}$ | 0.5 |
| 兴奋性时间常数 | $\tau_E$ | 1.0 |
| 兴奋性外部输入 | $h_E$ | 2.0 |
| 抑制性外部输入 | $h_I$ | 1.0 |

---

## 核心理论结果

在上述参数下，分析给出：

- **行列式差值**：$\Delta_M = (1 - M_{EE})(1 + M_{II}) + M_{EI} M_{IE} = 0.45 > 0$（振荡存在的必要条件）
- **临界时间常数**：$\tau_I^c = \dfrac{M_{II}+1}{M_{EE}-1} \tau_E = 3.0$
- **临界角频率**：$\omega_0 = \sqrt{\Delta_M / (\tau_E \tau_I^c)} \approx 0.387\ \text{rad/time}$
- **振荡周期**：$T_0 = 2\pi / \omega_0 \approx 16.22$
- **平衡点**：$(v_E^*, v_I^*) \approx (4.44,\ 4.22)$

当 $\tau_I < \tau_I^c$ 时，平衡点为**稳定焦点**；当 $\tau_I > \tau_I^c$ 时，平衡点失稳，系统出现**持续振荡**。

---

## 输出图像

程序运行后，将在 `./hopf_figs/` 目录下生成三张图：

### 图1：`fig1_phase_timeseries.png`
相平面轨迹与时间序列对比，覆盖三个典型 $\tau_I$ 取值：
- $\tau_I = 1.5$（稳定焦点，轨迹螺旋收敛）
- $\tau_I = \tau_I^c = 3.0$（临界状态，近似等幅振荡）
- $\tau_I = 5.4$（不稳定焦点，轨迹向外发散）

### 图2：`fig2_eigenvalue_crossing.png`
特征值实部 $\text{Re}(\lambda)$ 随 $\tau_I$ 的变化曲线，直观展示 Hopf 分叉点处实部从负变正的穿越行为。

### 图3：`fig3_hE_border_collision.png`
外部输入 $h_E$ 扫描分析，展示平衡点 $v_E^*$、$v_I^*$ 随 $h_E$ 的线性变化及 ReLU 边界碰撞（border-collision）效应，标注两个临界值：
- $h_E^c = M_{EI} h_I / (1 + M_{II})$（$v_E^*$ 碰零边界）
- $h_E^{c,I} = (M_{EE}-1) h_I / M_{IE}$（$v_I^*$ 碰零边界）

---


## 代码说明

- **`relu(z)`**：ReLU 激活函数 $[\cdot]_+$
- **`ei_ode_full(t, y, tau_I, hE, hI)`**：E-I 网络 ODE 右端，使用 `scipy.integrate.solve_ivp`（LSODA 方法）求解，相对精度 $10^{-6}$，绝对精度 $10^{-8}$
- 临界情况下排除前 80 个时间单位的瞬态，仅展示后期稳态行为
- 中文字体自动适配 macOS / Windows / Linux 环境

---

## 注意事项

- 临界点 $\tau_I = \tau_I^c$ 处的振荡为**近似等幅**，严格极限环的存在需借助中心流形理论与 Lyapunov 系数进一步确认。
- ReLU 非线性使系统在平衡点脱离激活区后行为发生质变，需注意参数范围的物理有效性（$v_E^* > 0$，$v_I^* > 0$）。
