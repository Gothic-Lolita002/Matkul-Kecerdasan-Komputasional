    import random
    import matplotlib.pyplot as plt
Bagian ini menunjukkan pemanggilan Library random dan matplotlib.pyplot
#
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
Bagian ini menunjukkan list data barang, Batasan Kapasitas (MAX_WEIGHT), serta Parameter 
Algoritma Genetika yang mengatur bagaimana pengembangan solusi akan berjalan:
#
INIT

    def create_individual():
        return [random.randint(0,1) for _ in items]
    
    def init_population():
        return [create_individual() for _ in range(POP_SIZE)]
Bagian ini menunjukkan proses pembentukan individu dan populasi awal, Fungsi create_individual()
Fungsi ini bertujuan untuk membuat satu solusi acak biasanya disebut individu, Fungsi init_population()
ini bertugas membuat kelompok besar dari individu-individu acak di atas untuk memulai proses evolusi.
#
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
Bagian ini menunjukkan Fungsi yang bertugas untuk memberikan nilai kualitas pada setiap kandidat solusi (individu).
#
SELECTION

    def selection(pop):
        return max(random.sample(pop, 3), key=fitness)
Bagian ini menunjukkan Fungsi yang memastikan bahwa individu yang memiliki total nilai barang tinggi (dan berat di bawah 7) 
memiliki peluang lebih besar untuk terpilih sebagai induk, namun tetap membiarkan adanya unsur keberuntungan.
#
CROSSOVER

    def crossover(p1, p2):
        point = random.randint(1, len(p1)-1)
        return p1[:point] + p2[point:]
Bagian ini menunjukkan Fungsi crossover(p1, p2) yang dalam gambar tersebut adalah implementasi dari Single-Point Crossover (Persilangan Satu Titik). Ini adalah tahap "reproduksi" di mana dua orang tua (parents) digabungkan untuk menghasilkan anak (offspring) yang membawa sifat dari keduanya.
#
MUTATION

    def mutate(ind):
        for i in range(len(ind)):
            if random.random() < MUTATION_RATE:
                ind[i] = 1 - ind[i]
        return ind
Bagian ini menunjukkan Fungsi mutate(ind) yang ada pada gambar tersebut adalah tahap Mutasi. 
Jika tahap crossover sebelumnya bertujuan untuk menggabungkan sifat induk, 
maka mutasi bertujuan untuk memberikan perubahan acak guna menjaga variasi genetik dalam populasi.
#
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
HASIL

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
  

VISUAL

      plt.figure()
      plt.plot(best_hist)
      plt.plot(avg_hist)
      plt.title("Perkembangan Nilai (Fitness)")
      plt.xlabel("Generasi")
      plt.ylabel("Value")
      plt.legend(["Best", "Average"])
      plt.grid()
      plt.show()
  
  
RUN
  GA()
