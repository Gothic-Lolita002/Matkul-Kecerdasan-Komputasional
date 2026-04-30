    import random
    import matplotlib.pyplot as plt
    
DATA BARANG

    items = [
        ("Laptop", 3, 200),
        ("HP", 1, 100),
        ("Buku", 2, 60),
        ("Jaket", 2, 80),
        ("Kamera", 1, 150),
        ("Sepatu", 2, 70),
        ("Powerbank", 1, 40),
    ]
    
    MAX_WEIGHT = 7
    
    POP_SIZE = 50
    GENERATIONS = 100
    MUTATION_RATE = 0.2
    ELITE_SIZE = 2
vxvxv
#
INIT

    def create_individual():
        return [random.randint(0,1) for _ in items]
    
    def init_population():
        return [create_individual() for _ in range(POP_SIZE)]
    
FITNESS

    def fitness(individual):
        total_weight = 0
        total_value = 0
    
        for gene, (name, w, v) in zip(individual, items):
            if gene == 1:
                total_weight += w
                total_value += v
    
        # penalti jika overweight
        if total_weight > MAX_WEIGHT:
            return 0
    
        return total_value

SELECTION

    def selection(pop):
        return max(random.sample(pop, 3), key=fitness)
    
CROSSOVER

    def crossover(p1, p2):
        point = random.randint(1, len(p1)-1)
        return p1[:point] + p2[point:]

MUTATION

    def mutate(ind):
        for i in range(len(ind)):
            if random.random() < MUTATION_RATE:
                ind[i] = 1 - ind[i]
        return ind

ANALISIS

    def decode(ind):
        selected = []
        total_weight = 0
        total_value = 0
    
        for gene, (name, w, v) in zip(ind, items):
            if gene == 1:
                selected.append(name)
                total_weight += w
                total_value += v
    
        return selected, total_weight, total_value
    

GA

    def GA():
        pop = init_population()
    
        best_hist = []
        avg_hist = []
    
        print("\n===== PROSES EVOLUSI (KNAPSACK) =====\n")
    
        for gen in range(GENERATIONS):
    
            pop = sorted(pop, key=fitness, reverse=True)
    
            best = pop[0]
            best_fit = fitness(best)
            avg_fit = sum(fitness(p) for p in pop) / POP_SIZE
    
            best_hist.append(best_fit)
            avg_hist.append(avg_fit)
    
            if gen % 5 == 0:
                print(f"Gen {gen:3d} | Best Value: {best_fit} | Avg: {avg_fit:.2f}")
    
            new_pop = pop[:ELITE_SIZE]
    
            while len(new_pop) < POP_SIZE:
                p1 = selection(pop)
                p2 = selection(pop)
    
                child = crossover(p1, p2)
                child = mutate(child)
    
                new_pop.append(child)
    
            pop = new_pop
  
      # =====================================================
      # HASIL
      # =====================================================
      best = sorted(pop, key=fitness, reverse=True)[0]
      selected, weight, value = decode(best)
  
      print("\n===== HASIL AKHIR =====")
      print("Barang terpilih :", selected)
      print("Total berat     :", weight)
      print("Total nilai     :", value)
  
      if weight <= MAX_WEIGHT:
          print("Penjelasan: Solusi valid dan optimal.")
      else:
          print("Penjelasan: Melebihi kapasitas (tidak valid).")
  
      # =====================================================
      # VISUAL
      # =====================================================
      plt.figure()
      plt.plot(best_hist)
      plt.plot(avg_hist)
      plt.title("Perkembangan Nilai (Fitness)")
      plt.xlabel("Generasi")
      plt.ylabel("Value")
      plt.legend(["Best", "Average"])
      plt.grid()
      plt.show()
  
  
  # RUN
  GA()
