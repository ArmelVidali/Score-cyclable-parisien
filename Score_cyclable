from operator import itemgetter
import csv


voies_voitures = QgsProject().instance().mapLayersByName('voies_lambert')[0]
voies_velo = QgsProject().instance().mapLayersByName('reseau-cyclable-lambert')[0]
place_velo = QgsProject().instance().mapLayersByName('stationnement_lambert')[0]


polygon_layer = QgsProject().instance().mapLayersByName('arondissement_lambert')[0]

line_feats_voiture = [f for f in voies_voitures.getFeatures()]
idx_voiture = QgsSpatialIndex(voies_voitures.getFeatures())

line_feats_velo = [f for f in voies_velo.getFeatures()]
idx_velo = QgsSpatialIndex(voies_velo.getFeatures())

feats_places = [f for f in place_velo.getFeatures()]
idx_places = QgsSpatialIndex(place_velo.getFeatures())




def calcul_score_cyclable(features_couche, index_spatial):
    liste = []
    for p in polygon_layer.getFeatures():   
        candidates = index_spatial.intersects(p.geometry().boundingBox())
        candidate_feats = [f for f in features_couche if f.id() in candidates]
        score = 0
        compteur = 0
        for l in candidate_feats:
            values = [l[0], l[2], l[3], l[12] ]
            score += 5*(len(set(values) & set(excellent)))
            score += 2*(len(set(values) & set(bien)))
            score -= ((len(set(values) & set(dangeureux))))
            compteur += 1
        liste.append([p[1],round(score/compteur,4)])  
    return liste

def calcul_longueur_troncons(features_couche, index_spatial):
    liste = []
    for p in polygon_layer.getFeatures():    
        total_line_length = 0
        candidates = index_spatial.intersects(p.geometry().boundingBox())
        candidate_feats = [f for f in features_couche if f.id() in candidates]
        for l in candidate_feats:
            intersect = l.geometry().intersection(p.geometry())
            total_line_length += intersect.length()
        liste.append([p[1],round((1000000*total_line_length)/p[6],4)]) #longeur de tronconsen m /km² 
    return liste

def compte_place_velo(features_couche, index_spatial):
    liste = []
    for p in polygon_layer.getFeatures():    
        nombre_places = 0
        candidates = index_spatial.intersects(p.geometry().boundingBox())
        candidate_feats = [f for f in features_couche if f.id() in candidates]
        for l in candidate_feats:
            nombre_places += l[4]
        liste.append([p[1],round((1000000*nombre_places)/p[6],4)]) #nombre de place / km²
    return liste

def appliquer_note(liste):
    tri = sorted(liste, key=itemgetter(1))
    i = 1
    for sous_liste in tri:
        sous_liste.append(i)
        i += 1
    return tri


def export_csv(export, nom_export):
    with open(nom_export, "w", newline="") as f:
        writer = csv.writer(f)
        writer.writerows(export)

excellent = ["Pistes cyclables", "Zone 30", "Protégé", "Sens de circulation générale" ]
bien = ["Aire piétonne", "Voie 30" , "Marqué"]
dangeureux = ["Bandes cyclables","Voie 50", "Aire piétonne", "Contresens"]


score_troncon_velo = calcul_longueur_troncons(line_feats_velo , idx_velo)
score_cyclable = calcul_score_cyclable(line_feats_velo, idx_velo)
score_place = compte_place_velo(feats_places, idx_places)

a = appliquer_note (score_cyclable)
export_csv(a, "score_cyclable.csv")



b = appliquer_note(score_troncon_velo)
export_csv(b,"score_troncon_velo.csv")

c = appliquer_note(score_place)
export_csv(c , "score_place.csv")




